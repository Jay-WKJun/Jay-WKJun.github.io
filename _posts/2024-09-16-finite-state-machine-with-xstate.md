---
layout: post
title: Finite State Machine (with XState)
tags: [state machine, XState, JavaScript, Web, Frontend, state, state management]
excerpt_separator: <!--more-->
---

# Finite State Machine (with XState)

애플리케이션이 발전하다보면 수많은 state가 생기게 됩니다. 그러다보면 여러 state간의 관계가 생기고 복잡해집니다. 단순히 로직으로 이를 관리하다보면 코드가 복잡해지고 유지보수가 어려워집니다.

상태 하나가 추가되면 다른 상태들과의 관계를 고려해야하고, 그럼 상태 하나가 추가될 때마다 복잡도가 기하급수적으로 증가합니다.

이런 복잡한 문제를 깔끔하게 해결하기 위해, Finite State Machine을 사용해 보았습니다.

![statemachine]({{ "../assets/img/statemachine/statemachine.webp" | relative_url }})

<!--more-->

# State Machine

**State machine(상태 머신)**은 시스템이 가질 수 있는 모든 상태와 상태 간의 전환을 명확하게 모델링하는 개념입니다. 시스템은 **주어진 순간에 하나의 상태만** 가질 수 있으며, 특정 이벤트가 발생할 때 정의된 전환에 따라 다른 상태로 변경됩니다.

정리하면 상태 머신은 다음과 같은 특징을 가집니다.

1.  한번에 하나의 상태만 가질 수 있습니다.
2.  상태(state): 시스템이 가질 수 있는 모든 상태들을 정의합니다.
3.	이벤트(event): 상태 전이를 일으키는 트리거, 즉 상태 변화를 유도하는 조건입니다.
4.	전이(transition): 이벤트가 발생했을 때 시스템이 현재 상태에서 다른 상태로 이동하는 과정을 말합니다.

상태 머신은 일종의 프레임워크처럼 이벤트에 따른 상태를 명확하고 예측 가능하게 만들어, 복잡한 상태 관리를 단순화하는 데 유용합니다.

## Finite State Machine

이번에 사용하는 state machine은 정확히는 Finite State Machine입니다.

여기서 Finite는 유한한 이라는 뜻으로, 상태가 유한하다는 것을 의미합니다.

위키에 FSM의 [정의](https://ko.wikipedia.org/wiki/%EC%9C%A0%ED%95%9C_%EC%83%81%ED%83%9C_%EA%B8%B0%EA%B3%84)가 잘 정리돼있지만, 간단히 요약하면 **제한된 구조와 제한된 상태들을 몇가지 제약으로 복잡하게 얽힌 흐름을 단순하고 직관적으로 표현하는 방법**입니다.

반대로 Infinite라면 무한한 상태를 가지게 되는데, 현실적으로 무한한 상태를 단순하고 직관적으로 표현하는 것은 불가능합니다. 예시로 Finite State Machine을 쓰지 않은 날 것 그대로의 로직이 Infinite State Machine이라고 할 수 있을 것 같습니다.

때문에 애플리케이션에서는 보다 직관적이고 단순하게 상태의 흐름을 표현할 수 있는 Finite State Machine가 더 적합하다고 할 수 있습니다.

# XState

state machine을 구현하는 방법엔 여라가지가 있지만, 이번엔 TypeScript 환경에서 [XState](https://stately.ai/docs)라는 라이브러리를 사용했습니다. (V.5 버전을 기준으로 합니다.)

XState는 JS / TS로 작성된 라이브러리로 State Machine을 정의하고, 상태 전이를 관리하는 데 도움을 주는 라이브러리입니다.

굉장히 장점이 많은 라이브러리라고 생각합니다. 편리한 디버깅 툴(Inspector, XState VS Code extension), 정의한 상태 머신을 시각적으로 보여주고 테스트 해볼 수 있는 툴 [Stately Visualizer](https://stately.ai/viz), 풍부한 예시(Stately Studio)들이 있어 개발 편의성이 높습니다.

또한, React, Vue, Svelte 등의 UI 라이브러리 혹은 프레임워크들과도 쉽게 연동할 수 있습니다.

# Example

XState로 간단한 Finite State Machine을 구현해보기 위해 간단한 예시를 만들어 구현해보았습니다.

## 예시 시나리오

예시는 결제 진행으로 크게 1. 결제 준비 2. 결제 진행 단계로 나뉩니다.

결제 준비 단계에선 비회원, 회원 상태를 가지고 있고, 비회원과 회원 상태에 따라 결제 준비 단계가 약간 다르게 진행됩니다.

그리고 결제 진행 단계에선 비동기 처리 상태에 따라 상태와 UI가 변경되고 결제가 완료되면 종료됩니다.

### Demo

간단히 어떤 예시인지 완성된 데모를 살펴보겠습니다.

non-member | member
------------ | -------------
![non-member]({{ "../assets/img/statemachine/non_member_example.gif" | relative_url }}) | ![member]({{ "../assets/img/statemachine/member_example.gif" | relative_url }})

## 상태 머신이 없을 때

먼저 상태 머신이 없을 때의 코드를 살펴보겠습니다.

상태 머신이 상태를 관리하지 않기 때문에, 우선 상태를 선언해야 합니다. 필요한 상태는 다음과 같습니다.

```tsx
  // 회원인가 아닌가?
  const [isMember, setIsMember] = useState(false);

  // 동의절차는 정의된 순서 그대로 순차적으로 진행됩니다.
  type AgreeProcess = 'user-info-input' | 'user-info-consent' | 'payment-consent' | 'payment';
  // 동의 절차를 하나씩 순차적으로 하나씩 추가해서 보여줘야 함.
  const [agreeProcess, setAgreeProcess] = useState<AgreeProcess | undefined>();
```

비회원이면 정보 입력을 위한 입력창을 띄워줍니다. 입력이 완료되면 회원과 동일한 동의 절차를 시작합니다.

여기서 현재 state에 따라서 다음 절차를 파악하는 등, 동의 절차 state를 컴포넌트에서 모두 수동으로 관리해야합니다.

```tsx
  // 회원인 경우와 아닌 경우 시작하는 동의 절차 상태가 다름
  <Button onClick={() => {
    // 현재 상태에 따라 느슨하게 연결된 상태 변경 로직
    if (isMember) {
      setAgreeProcess('user-info-consent');
    } else {
      setAgreeProcess('user-info-input');
    }
  }}>
    결제를 진행합니다.
  </Button>
```

```tsx
  // 상태에 따른 동의 절차 컴포넌트 추가
  const agreeComponents: ReactNode[] = [];

  if (!isMember) {
    agreeComponents.push(
      <InputContainer
        key="user-info-input"
        onClick={() => {
          setAgreeProcess('user-info-consent');
        }}
      />
    );
  }

  if (agreeProcess === 'user-info-consent') {
    agreeComponents.push(
      <div key="user-info-consent">
        <FormControlLabel
        control={
          <Checkbox
            onChange={(e) => {
              const { checked } = e.target;
              if (checked) {
                setAgreeProcess('payment-consent');
              }
            }}
            color="primary"
          />
        }
        label="사용자 정보에 동의하시겠습니까?"
        />
      </div>
    );
  }

  // 동일하게 진행...
```

결제 동의 절차가 끝나고 본격적으로 결제 요청이 시작되는 비동기 작업의 경우 아래와 같은 코드로 관리됩니다.

```tsx
// 결제 요청 여부
  const [isFetched, setIsFetched] = useState(false);
  // 비동기 결제를 요청하고 3초를 client에서 스스로 측정해서 보여줘야 함.
  const [isLoading, setIsLoading] = useState(false);
```

동일하게 모든 상태 변화를 native 로직을 통해 관리합니다.

```tsx
  const handlePaymentButtonClick = useCallback(async () => {
    // 모든 상태 변화를 수동으로 관리
    setIsFetched(true);

    const loadingTimer = setTimeout(() => {
      setIsLoading(true);
    }, 3000);

    try {
      await mockPaymentFetching(() => {
        clearTimeout(loadingTimer);
        setIsFetched(false);
        setIsLoading(false);
      });
    } catch {
      clearTimeout(loadingTimer);
      setIsFetched(false);
      setIsLoading(false);
    }
  }, []);

  // 결제 시작 버튼
  <Button variant='contained' onClick={() => {
    handlePaymentButtonClick();
  }}>결제 시작</Button>

  // 결제 진행 상태 표시 모달
  <Dialog open={isFetched}>
    <Box>
      <CircularProgress />
      {isLoading ? <div style={{ color: 'red' }}>잠시만 기다려 주세요...</div> : <div>결제중...</div>}
    </Box>
  </Dialog>
```

상위 코드는 모든 상태관리 로직과 View가 한 곳에 모여있어 가독성 및 관리가 어렵습니다.

또한, 엄격한 규칙없이 native 로직으로 느슨하게 관리하다보면, 코드의 의도를 알기도 어렵고 이로인해 잘못된 코드 수정으로 버그가 발생할 여지가 많아져 유지보수가 어려워집니다. (협업의 어려움 가중)

## 상태 머신을 사용할 때

이제 XState를 사용하여 상태 머신을 구현하고 상위 코드를 리팩토링해보겠습니다.

먼저 결제 동의 절차를 위한 상태 머신을 정의합니다.

```tsx
export const paymentMachine = createMachine({
  id: 'payment',
  initial: 'Init',
  context: {
    // 이 사람이 회원인지 아닌지 기억한다.
    isMember: false,
  },
  states: {
    // !! : 명확한 상태 순서 정의
    Init: {
      on: {
        next: [
          {
            // 회원일 때만 유저 동의 절차로 이동
            guard: (context) => context.context.isMember,
            target: 'UserInfoConsent',
          },
          {
            // 비회원일 땐 유저 정보 입력으로 이동
            guard: (context) => !context.context.isMember,
            target: 'UserInfoInput',
          },
        ],
        stay: {
          target: 'Init',
        },
        setMember: {
          actions: assign({
            isMember: ({ context, event }) => event?.isMember ?? context.isMember
          }),
        }
      },
    },
    UserInfoInput: {
      on: { next: 'UserInfoConsent' },
    },
    UserInfoConsent: {
      on: { next: 'PaymentConsent' },
    },
    PaymentConsent: {
      on: { next: 'Payment' },
    },
    Payment: {
      type: 'final',
    },
  },
});
```

이제 상태 머신을 컴포넌트에 적용해보겠습니다.

```tsx
  import { useMachine } from '@xstate/react';

  // !! : 필요한 여러 상태들이 컴포넌트에서 보이지 않음.
  const [snapshot, send] = useMachine(paymentMachine, {});

  const curState = snapshot.value;
  const isMember = snapshot.context.isMember;

  // 상태에 따른 동의 절차 컴포넌트 추가
  const agreeComponents: ReactNode[] = [];

  if (!isMember) {
    agreeComponents.push(
      <InputContainer
        key="user-info-input"
        onClick={() => {
          console.log('click')
          send({ type: 'next' })
        }}
      />
    );
  }

  if (curState === 'UserInfoConsent') {
    agreeComponents.push(
      <div key="user-info-consent">
        <FormControlLabel
        control={
          <Checkbox
            color="primary"
            onChange={(e) => {
              const { checked } = e.target;
              if (checked) {
                // !!: 추상화 된 상태 머신 변환 메소드
                send({ type: 'next' });
              }
            }}
          />
        }
        label="사용자 정보에 동의하시겠습니까?"
        />
      </div>
    );
  }

  // 동일하게 진행...
```

결제 요청 비동기 작업의 경우는 아래와 같이 변경됩니다.

```tsx
// 로딩표시를 위한 상태 머신으로 항상 재사용할 수 있도록 설계
// 머신 종료 상태인 type: final을 적용하면 상태 머신 재사용이 힘듦.
export const paymentLoadingMachine = createMachine({
  id: 'payment',
  initial: 'Init',
  states: {
    Init: {
      on: { next: 'PaymentSend' },
    },
    PaymentSend: {
      on: { next: 'Init' },
      after: {
        // !! : 내부적인 상태 전환을 컴포넌트에서 해줄 필요가 없음.
        3000: 'Loading' // 3000ms (3초) 후 Loading으로 자동 전환
      },
    },
    Loading: {
      on: { next: 'Init' },
    }
  }
});
```

상태 머신을 컴포넌트에 적용합니다.

```tsx
  // 결제 요청 했다고 가정하는 비동기 함수
  async function mockPaymentFetching() {
    return new Promise((resolve) => {
      setTimeout(() => {
        console.log('done');
        resolve('done');
      }, 5000);
    });
  }

  const [paymentLoadingSnapshot, paymentLoadingSend] = useMachine(paymentLoadingMachine, {});

  const isFetched = paymentLoadingSnapshot.value !== 'Init';
  const isLoading = paymentLoadingSnapshot.value === 'Loading';
  const isDialogOpen = isFetched;

  const handlePaymentButtonClick = useCallback(async () => {
    if (isFetched) return;

    // !! : 훨씬 간결해진 코드
    paymentLoadingSend({ type: 'next' });
    try {
      await mockPaymentFetching();
      paymentLoadingSend({ type: 'next' });
    } catch {
      paymentLoadingSend({ type: 'next' });
    }
  }, [isFetched, paymentLoadingSend]);

  <Button variant='contained' onClick={() => {
    handlePaymentButtonClick();
  }}>결제 시작</Button>

  <Dialog open={isDialogOpen}>
    <Box>
      <CircularProgress />
      {isLoading ? <div style={{ color: 'red' }}>잠시만 기다려 주세요...</div> : <div>결제중...</div>}
    </Box>
  </Dialog>
```

상태 머신을 적용했을 때, 상태 머신의 엄격한 규칙으로 인해 코드의 의도가 더 명확해지고, 컴포넌트에서 상태를 다루는 여러 복잡한 로직들이 많이 사라졌습니다.

# Finite State Machine(w. XState) 후기

이번에 간단한 예시 상황을 만들고 XState로 코드를 리팩토링 해보면서 상태 머신의 장점과 단점을 경험했습니다.

## 장점

제가 느낀 **장점은 꽤 컷으며**, 상태 머신만으로도 웹 애플리케이션의 모든 상태를 관리할 수 있을 정도로 장점이 강력한 도구였다고 느꼈습니다.

명확한 상태 표현으로 **협업에도 큰 도움**이 될 것 같다고 생각했습니다.

- 명확한 상태의 흐름과 상태간의 관계 표현으로 인한 가독성 향상.
- 불가능한 상황을 방지하는 엄격한 규칙으로 인한 버그 방지.

## 단점

처음 경험해 본 FSM을 설계함에도 고려해야될 것이 많아 복잡했고, XState를 처음 사용할 때 쉽지 않았습니다.

FSM을 구현하기 위한 메소드와 제약 사항 같은 옵션들이 정말 많아서, 이를 확인하고 익히는 데만 시간이 좀 걸렸습니다.

이런 학습 비용은 FSM과 XState의 단점이라고 생각합니다.

또한 마치 리덕스처럼 간단한 상태 머신을 만드는데도 필요한 코드가 많아 처음 상태 머신을 만들 때 조금 번거로웠습니다.

필요한 코드가 많고 적용할 수 있는 옵션들이 만큼 설계가 잘못되어 여러 context가 얽히게 되면 상태 머신을 사용하지 않을 때보다 더 복잡해질 수 있다고 느꼈습니다.

따라서 상태 머신은 Visualizer 툴등을 이용해 디버깅 및 관리하면서 간결하게 유지하는 것이 중요하다고 생각했습니다.

- 상태 머신과 XState를 잘 활용하기 위해선 어느정도 학습 시간이 필요. (학습 비용 있음)
  - 팀 간 노하후 공유로 어느정도 완화 가능.
- redux처럼 상태 머신만을 위한 boilerplate 코드가 꽤 많이 필요하다.
  - 스니펫 활용으로 생산성 향상 가능.
- 상태 머신을 사용할 때는 상태 머신의 상태를 최대한 직관적이고 간결하게 유지하고, 상태 머신에 불필요한 context가 서로 얽히지 않도록 주의해야한다.
  - Visualizer를 잘 활용하면 복잡한 상태 머신을 시각적으로 확인할 수 있어 디버깅과 리팩토링에 도움이 될 수 있다.

# Conclusion

XState에선 FSM 구현을 위해 제공하는 메소드와 기능들이 정말 많지만, 이번 포스팅에선 정말 기본적인 기능들만 사용해보았습니다.

아직 잘 모르는 기능과 옵션들도 많아 앞으로 계속해서 사용해보며 익혀나가야겠습니다. (필요하다면 후속 포스팅에서 이어나가보겠습니다.)

처음에 익숙해지기는 어렵지만, 장점이 뚜렷하여 적절히 활용하면 점점 더 성장하고 복잡해지는 프로덕트 유지보수에 큰 도움이 될 것 같습니다.

감사합니다. 🙏

# ref

- [XState](https://xstate.js.org/docs/)

- [자바스크립트로 만든 유한 상태 기계 XState - 카카오 엔터테인먼트](https://fe-developers.kakaoent.com/2022/220922-make-cart-with-xstate/)

- [Introduction to State Machines in React with XState - GeekyAnts](https://geekyants.com/blog/introduction-to-state-machines-in-react-with-xstate)

- [Guidelines for State Machines and XState - Kyle Shevlin](https://kyleshevlin.com/guidelines-for-state-machines-and-xstate/)

- [Sustainable xState machines - dev.to](https://dev.to/gtodorov/sustainable-xstate-machines-2065)

- [유한 상태 기계 - 위키피디아](https://ko.wikipedia.org/wiki/%EC%9C%A0%ED%95%9C_%EC%83%81%ED%83%9C_%EA%B8%B0%EA%B3%84)

- [Testable XState-Machines: Combining XState with Dependency Inversion - Christoph Fricke](https://christoph.hashnode.dev/xstate-dependency-inversion)
