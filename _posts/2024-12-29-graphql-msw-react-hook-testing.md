---
layout: post
title: React Hook Testing - GraphQL(Apollo Framework) 환경에서 MSW 적용하기
tags: [react, react hook, react hook testing, msw, GraphQL, unit test, test, apollo framework]
excerpt_separator: <!--more-->
---

![unit_test_meme]({{ "../assets/img/aboutHookTest/write_unit_test_meme.jpg" | relative_url }})

React와 GraphQL(Apollo Framework) 환경에서 MSW(Mock Service Worker)를 활용한 React Hook 테스트 방법에 대해 알아보겠습니다.

또한, 테스트 구축이 프로젝트에 어떤 긍정적인 영향을 주는지까지 확인해보도록하겠습니다.

<!--more-->

# 테스트코드와 환경 구축

JavaScript에서 순수 함수를 테스트하는 것은 비교적 간단합니다. 하지만 특정 환경에서 실행되며 내부적으로 side effect를 일으키는 함수라면, 테스트 환경에서 그 환경을 mocking해야 합니다.

React Hook은 하나의 함수처럼 보이지만, React라는 특수한 환경에서 동작하기 때문에 테스트 시 **React 환경을 mocking**하는 작업이 필요합니다.

여기서 더 나아가 **Apollo Framework를 사용한 GraphQL 요청**까지 포함된다면, **이를 처리할 테스트 환경**도 구축해야 합니다.

**msw도 GraphQL 통신 환경에 맞춰 환경을 구축**해야합니다.

# 이전 코드 - Query에 종속된 컴포넌트

기존에 있던 코드를 먼저 확인하고 이를 리팩토링 하면서 hook 테스트를 구축해보겠습니다.

기존 코드는 **컴포넌트에 query와 함께** 있어, 서로 종속된 구조로 **query만 따로 테스트할 수 없는 구조**입니다.

컴포넌트를 통째로 storybook + msw로 테스트할 수 있지만, 컴포넌트 안엔 여러 query들이 뒤섞여있을 수 있어 **개별적인 query에 대한 테스팅은 불가능**합니다.

이렇게, View와 여러 Query들을 한꺼번에 테스트할 수 밖에 없어 **디버깅도 불편**합니다.

```tsx
import { gql, useQuery } from "@apollo/client";

const ReportScreenQuery = gql`
  query Report($reportId: ID!) {
    examReport(reportId: $reportId) {
      id
      createdAt
      exam {
        id
        createdAt
        finishedAt
        problems
        answers
        examinee {
          id
          name
          scholarLevel
        }
      }
      result {
        id
        score
        grade
      }
    }
  }
`

interface ReportScreenProps { reportId: string; }
function ReportScreen(props: ReportScreenProps) {
  // component가 query에 구속된 형태
  const { data } = useQuery(ReportScreenQuery, { variables: { reportId: '1' } });

  // component 내부에서 직접 data를 가공
  const examReport = data?.examReport;
  const exam = examReport?.exam;
  const examinee = exam?.examinee;
  const result = examReport?.result;

  return (
    <div>
      <div>
        {/* 시험 결과 렌더링... */}
        {...result}
      </div>
      <div>
        {/* 시험 본 사람의 정보 렌더링... */}
        {...examinee}
      </div>
      <div>
        {/* 시험 정보 렌더링... */}
        {...exam}
      </div>
    </div>
  )
}
```

# custom hook과 Query 그리고 테스트 구축

component에 있던 Query를 custom hook에서 하도록 변경하여 의존성을 변경해보겠습니다.

```typescript
// imports...(생략)
const ReportScreenQuery = gql`...query`;

export function useReportQuery(reportId: string) {
  const res = useQuery(ReportScreenQuery, { variables: { reportId } });
  const { data } = res;

  const examReport = data?.examReport;
  const exam = examReport?.exam;
  const examinee = exam?.examinee;
  const result = examReport?.result;

  // 메소드를 통해 정보를 가공해 제공
  const isReportCreatedBeforeGivenTime = useCallback((time: number) => {
    return exam.createdAt <= time;
  }, [exam]);

  const getExamineeScholarLevel = useCallback(() => {
    return examinee.scholarLevel
  }, [examinee]);

  // 훅 안에서 정보를 가공해 밖에 제공
  return {
    ...res,
    examReport,
    exam,
    examinee,
    result,
    isReportCreatedBeforeGivenTime,
    getExamineeScholarLevel,
  }
}
```

이 hook을 가지고 다양한 component에서 동일한 Query를 재사용할 수 있게 되었습니다.

## 테스트 구축 - renderHook

custom hook을 생성했으니 이제 테스트를 구축해보겠습니다.

**hook의 query가 잘 요청되고 제대로 응답값이 오는지**, 결과 데이터를 조작하는 각 메소드들이 의도에 맞게 **정보를 정확히 제공하는지 확인**합니다.

본 포스팅에선 `import { renderHook } from '@testing-library/react'`을 활용해 hook만 별도로 테스트합니다.

```typescript
import React from 'react';
import { renderHook } from '@testing-library/react';
import { useReportQuery } from './useReportQuery';

describe('useReportQuery', () => {
  test('useReportQuery test', async () => {
    // Report 키 1
    const { result } = renderHook(() => useReportQuery('1'));
    console.log('result : ', result.current.data);
  });
});
```

하지만, hook 안에 Apollo framework의 GraphQL query 요청이 있으므로, 이를 mocking 해줘야합니다.

### GraphQL 연결 (Apollo framework)

아래와 같이 수정하면 hook 내부의 useQuery가 정상 실행되며 **GraphQL 서버와 통신**할 수 있습니다.

```typescript
import React from 'react';
import { renderHook } from '@testing-library/react';
import { ApolloClient, InMemoryCache, ApolloProvider, HttpLink } from '@apollo/client';
import fetch from 'cross-fetch';
import { useReportQuery } from './useReportQuery';

describe('useReportQuery', () => {
  test('useReportQuery test', async () => {
    // 추가: Apollo framework 환경
    const wrapper = ({ children }: any) => {
      const client = new ApolloClient({
        uri: 'https://somewhere...',
        cache: new InMemoryCache(),
        link: new HttpLink({ uri: 'https://somewhere...', fetch }),
      });

      return <ApolloProvider client={client}>{children}</ApolloProvider>;
    };

    const { result } = renderHook(() => useReportQuery('1'), { wrapper });
    // 추가: query가 끝날때까지 대기
    await waitFor(() => {
      expect(result.current.data).toBeTruthy();
    });

    console.log('result : ', result.current.data);
  });
});
```

## msw 추가

server가 항상 안정적으로 통신된다면 좋겠지만, 항상 그렇지 못합니다.

그럼 테스트 할 수 없게 되는데요, 이를 대비해 server를 대신할 msw를 추가해줍니다.

msw는 서버를 mocking하기 때문에, **실제 서버 상태와 무관하게 테스트를 안정적으로 실행**할 수 있습니다. 또한 코드와 테스트 코드 자체를 수정할 필요 없이 **msw 설정만 업데이트**하면 됩니다.

```typescript
// mockStore.ts
// MockStore 객체에 GraphQL Api의 Query를 mocking해 등록하고 msw handler를 정의합니다.

import { GraphQLContext, GraphQLRequest, GraphQLVariables, ResponseResolver } from 'msw';
// 해당 Import type은 GraphQL Api가 가지고 있는 사용가능한 모든 Query를 TypeScript 코드로 Generate한 type입니다.
// code Generator를 사용했습니다. (생성되는 코드는 저와 다를 수 있습니다. 생성된 코드에 적절히 맞추어 수정해 사용해주세요.)
import type { Queries } from './query.generated';

type QueryKeys = keyof Queries;
type Context = { [key: string]: any };
type Resolver<T extends QueryKeys, K extends unknown> = { [T]: ({ variables?: Queries[T].variables, context?: K }) => { data: Queries[T].query, context?: K } }
type Variable<T extends QueryKeys> = Queries[T].variables;

export class MockStore {
  #resolver: Resolver;
  #context?: Context;
  constructor(resolver: Resolver, context?: Context) {
    this.#resolver = resolver;
    this.#context = context;
  }

  async #resolve<T extends QueryKeys>(operationName: T, variables: Variable<T>) {
    const resolver = this.#resolver[operationName];

    if (resolver == null) {
      throw new Error(`No resolver for operationName: ${String(operationName)}`);
    }

    let result: any = undefined;
    let errors: GraphQLTestError[] = [];

    try {
      result = await resolver({ variables, context: this.#context });
    } catch (error) {
      if (error) errors = [...errors, error as GraphQLTestError];
    }

    return { data: result, errors: errors.length >= 1 ? errors : undefined };
  }

  mswHandler: ResponseResolver<GraphQLRequest<GraphQLVariables>, GraphQLContext<Record<string, any>>, any> =
    async (req, res, ctx) => {
      const operationName = (req as any).operationName ?? req.body?.operationName;

      const { data, errors } = await this.#resolve(operationName, req.variables as any);

      return res(ctx.data(data as any), ctx.errors(errors));
    };
}
```


```typescript
import { setupServer } from 'msw/node';
import { graphql } from 'msw';
import { MockStore } from './mockStore';

const store = new MockStore({
  ReportQuery: () => ({
    __typename: "Query",
    examReport: {
      id: "report_12345",
      createdAt: "2025-01-01T12:00:00Z",
      exam: {
        id: "exam_98765",
        createdAt: "2024-12-31T10:00:00Z",
        finishedAt: "2024-12-31T11:00:00Z",
        problems: [
          "What is the capital of France?",
          "Solve for x: 2x + 3 = 7",
          "Describe the process of photosynthesis."
        ],
        answers: [
          "Paris",
          "x = 2",
          "Photosynthesis is the process by which green plants and some other organisms use sunlight to synthesize foods with the help of chlorophyll."
        ],
        examinee: {
          id: "examinee_56789",
          name: "John Doe",
          scholarLevel: "Undergraduate"
        }
      },
      result: {
        id: "result_34567",
        score: 95,
        grade: "A"
      }
    }
  })
})

// 서버 초기화
const server = setupServer();

beforeAll(() => {
  server.listen();
});
beforeEach(() => {
  // 서버에 GraphQL operation을 사용하도록 등록
  server.use(graphql.operation(store.mswHandler));
});
afterEach(() => {
  // 초기화
  server.resetHandlers();
});
afterAll(() => {
  server.close();
});
```

# 최종 테스트 코드

완성된 테스트 코드를 정리합니다.

```typescript
// useReportQuery.test.ts
import React from 'react';
import { renderHook } from '@testing-library/react';
import { ApolloClient, InMemoryCache, ApolloProvider, HttpLink } from '@apollo/client';
import fetch from 'cross-fetch';
import { setupServer } from 'msw/node';
import { graphql } from 'msw';
import { useReportQuery } from './useReportQuery';
import { MockStore } from './mockStore';

// mock data 설정
const store = new MockStore({
  ReportQuery: () => ({ ...mockData })
})

// 서버 초기화
const server = setupServer();

beforeAll(() => {
  server.listen();
});
beforeEach(() => {
  // 서버에 GraphQL operation을 사용하도록 등록
  server.use(graphql.operation(store.mswHandler));
});
afterEach(() => {
  // 초기화
  server.resetHandlers();
});
afterAll(() => {
  server.close();
});

describe('useReportQuery', () => {
  test('useReportQuery test', async () => {
    // 추가: Apollo framework 환경
    const wrapper = ({ children }: any) => {
      const client = new ApolloClient({
        uri: 'https://somewhere...',
        cache: new InMemoryCache(),
        link: new HttpLink({ uri: 'https://somewhere...', fetch }),
      });

      return <ApolloProvider client={client}>{children}</ApolloProvider>;
    };

    const { result } = renderHook(() => useReportQuery('1'), { wrapper });
    // 추가: query가 끝날때까지 대기
    await waitFor(() => {
      expect(result.current.data).toBeTruthy();
    });

    console.log('result : ', result.current.data);
  });
});
```

# 정리

지금까지 GraphQL Query를 custom hook에서 다루도록 리팩토링하고 그 hook을 테스트 하는 방법에 대해 알아봤습니다.

GraphQL Query를 custom hook에서 다루도록 리팩토링하면서 코드 유지보수성과 테스트 가능성 모두에서 큰 이점을 얻을 수 있었습니다.
-	custom hook을 활용한 구조적 개선
	-	**각 Query에 대해 독립적인 테스트 코드**를 작성할 수 있게 되었습니다.
	-	**재사용 가능한 hook**을 통해 불필요한 prop drilling을 방지했습니다.
-	비즈니스 로직 집중화
	-	hook 내부에서 Query 결과를 조작하는 함수들을 제공해 **비즈니스 로직이 흩어지는 것을 방지**했습니다.
	-	**각 함수에 대한 테스트 코드 적용**으로 안정성을 더욱 강화했습니다.
-	테스트의 부가적 효과
	-	**작은 단위의 코드에서 안정성을 확보**할 수 있었습니다.
	-	**테스트는 로직에 대한 설명서 역할**을 하며, 문제 상황 발생 시 팀원 간의 논의를 더욱 명확히 하고 근거를 제공하는 도구로 활용될 수 있습니다.

이러한 접근 방식은 유지보수성을 높이고 협업을 원활하게 할 뿐 아니라, 더 나은 사용자 경험을 제공하기 위한 중요한 밑거름이 될 것입니다.

여러분도 이런 접근 방식을 시도해 보며 코드의 가치를 한 단계 더 끌어올려 보시길 바랍니다.

감사합니다!
