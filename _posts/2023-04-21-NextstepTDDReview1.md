---
layout: post
title: 너와 나와 우리를 위한 클린코드 (feat. NextStep, TDD 클린코드 with React 1)
tags: [NextStep, TDD, react, cleanCode, 클린코드, 리액트, 클린아키텍처]
excerpt_separator: <!--more-->
---

# NextStep, TDD 클린코드 with React

![cleanCodeReact]({{ "../assets/img/cleanCodeReact/cleanCodeReact.jpeg" | relative_url }})

2월 ~ 4월까지 약 2달 동안 [NextStep](https://edu.nextstep.camp/)의 [TDD 클린코드 with React](https://edu.nextstep.camp/c/QoTvUh4y/) 과정을 진행했습니다.

과정을 진행하면서 여러모로 배운 점들이 정말 많지만, 이번엔 리액트 클린코드 과정 진행과 클린코드에 대해 배우고 느꼈던 점들에 대해 나누어보려고 합니다. 😁

<!--more-->

# 과정 소개

간단하게 제가 진행했던 TDD 클린코드 with React 2기 과정에 대해 설명하겠습니다.

미션은 크게 2가지로 구성되어있습니다.

## 페이먼츠 미션

![payments_demo]({{ "../assets/img/cleanCodeReact/payments_demo.gif" | relative_url }})

다수의 input이 있는 복잡한 form을 다루는 미션입니다.

**CDD 방법론**을 적용해 **Bottom-up 방식으로 컴포넌트를 개발**하고 **Storybook으로 단위 컴포넌트를 테스트**하는 것을 중점으로 합니다.

(이 미션에선 상태 관리 라이브러리를 사용할 수 없습니다. ㅎㅎ 😯)

-> TODO: PR이랑 github repo 주소 첨부

## 장바구니 페이지

![card_demo]({{ "../assets/img/cleanCodeReact/cart_demo.gif" | relative_url }})

Api가 없는 상황에서도 안정적으로 개발환경을 구축하고 복수의 페이지에서도 복잡한 state를 안정적으로 관리하는 방법을 중점으로 진행합니다.

전 **MSW**와 **react-query**를 적용해 개발했습니다.

-> TODO: PR 주소 첨부

## 이번 포스트는 페이먼츠 미션을 중심으로 진행됩니다.

두가지 미션 중에선 개인적으로 페이먼츠가 더 어려웠고 그만큼 배운 점도 더 많았다고 생각합니다.

따라서, 페이먼츠 미션을 위주로 이번 포스트를 진행하도록 하겠습니다!

# 페이먼츠 설계

페이먼츠 미션을 보고 처음에 생각했던 설계입니다.

이번 미션에선 상태 관리 라이브러리를 사용할 수 없어 전역 상태 공유는 모두 Context Api를 활용했습니다.

## 어디에서든 손쉽게 사용할 수 있도록 input Element와 동기화 된 객체를 state로 가져보자.

각 input Element에 대응하는 State 객체를 만들어 사용하고 메소드로 필요한 데이터를 가져올 수 있도록 했습니다.

페이먼츠 과제의 요구사항 중에 다음과 같은 사항이 있었고,

- 각 input들의 조건에 따라 validation 체크
- 유효한 값 입력시 다음 필드로 Input Focusing

페이먼츠 미션의 요구사항을 보고 생각했을 때, 각 input에 대응하는 state를 객체의 형태로 다룬 이유는 다음과 같습니다.

1. 스스로 validation하는 state 객체
  각 Input마다 각자의 validation 로직이 필요합니다.
  따라서 객체로 함께 관리하면 컴포넌트에서 별도의 실행로직 없이 새롭게 값이 갱신될 때마다, 자동으로 validation 할 수 있어 **컴포넌트의 로직을 한층 깔끔하게 할 수 있을 것이라고 생각**했습니다.
2. invalid에 곧바로 대응해 focus할 수 있는 state 객체
  Auto focus를 설계할 당시 invalid한 input을 찾아, focus하는 방법을 생각했습니다.
  따라서, **invalid한 state 객체를 찾으면 해당 input Element에 곧바로 focus할 수 있도록** HTMLInputElement를 포함시켜두면 좋을 것 이라고 생각했습니다.
3. 컴포넌트의 로직을 깔끔하게 할 수 있을 것으로 기대했습니다.
  input의 UI들은 비슷했기에, 구체적인 비즈니스 로직을 state 객체에 넣어두면 **공통 컴포넌트에 UI를 정의하고 객체를 주입해 간편하게 재사용할 수 있을 것**으로 기대했습니다.

### 구현

아래는 코드로 구현한 결과물 입니다.

(원래 코드를 조금 수정했습니다.. 추상화단계가 더 있지만, 글의 가독성을 위해 로직을 좀 더 직관적으로 수정했습니다. ㅎㅎ)

```typescript
// input Element에 대응하는 객체 생성
type TCardNumber = string;

export class CardNumberInputElement {
  value?: TCardNumber;
  errorMessage?: string;
  ref?: HTMLInputElement | null;

  setRef(ref?: HTMLInputElement | null) {
    this.ref = ref;
  }

  // validate 로직, invalidate case에 따른 errorMessage와 함께 관리합니다.
  validateValue(value?: string) {
    if (isNil(value)) return;
    if (!value || value.length !== 4) {
      return '카드 번호 4자리를 입력해주세요.';
    }
  }

  isAllowToFocusNext() {
    return this.value?.length === 4;
  }

  checkWhetherThisUserInputCanBeSet(inputValue: TCardNumber) {
    return !filteredNumber || filteredNumber.length <= 4;
  }

  constructor({ value, ref }: Partial<CardNumberInputElement>) {
    this.value = value;
    this.errorMessage = this.validateValue(value);
    this.ref = ref;
  }
}

// cardStore에 합쳐서 Context로 제공합니다.
const cardStore = {
  cardNumbers: createArray(4, () => new CardNumberInputElement({})),
  cardOwners: createArray(1, () => new CardOwnerInputElement({})),
};
```

컴포넌트에서 아래와같이 적용됩니다.

```tsx
interface CardNumbersInputListProps {
  cardNumbers?: TCardStore['cardNumbers'];
}

export const CardNumbersInputList = memo(function CardNumbersInputList({ cardNumbers }: CardNumbersInputListProps) {
  const errorMessage = cardNumbers?.find((cardNumber) => !!cardNumber.errorMessage)?.errorMessage;

  return (
    <CardInputWrapper header="카드 번호" errorMessage={errorMessage}>
      <div className="input-box">
        {cardNumbers?.map((cardNumber, i) => {
          const isLast = checkIsArrayLast(cardNumbers, i);
          const isPasswordType = i >= cardNumbers.length - 2;
          return (
            <CardNumberInput
              key={`cardNumber-input-${i}`}
              type={isPasswordType ? 'password' : 'text'}
              needDividerRender={!isLast}
              cardNumber={cardNumber}
              index={i}
            />
          );
        })}
      </div>
    </CardInputWrapper>
  );
});

interface CardNumberProps {
  type?: HTMLInputTypeAttribute;
  cardNumber: CardNumberInputElement;
  index: number;
  needDividerRender: boolean;
}

export const CardNumberInput = memo(function CardNumberInput({
  type = 'text',
  cardNumber,
  index,
  needDividerRender,
}: CardNumberProps) {
  const { checkWhetherThisUserInputCanBeSet, value, setRef, errorMessage } = cardNumber;
  const isError = !!errorMessage;

  const cardContextApis = useCardContextApis();

  const onInputChange = {
    if (checkWhetherThisUserInputCanBeSet(e.currentTarget.value)) {
      const newValue = filterNumber(e.currentTarget.value);
      cardContextApis?.dispatch({ type: 'cardNumbers', payload: { index, value: newValue } });
    }
  };

  const handleCardNumberInputFocus = () => {
    cardContextApis?.dispatch({ type: 'cardNumbers', payload: { index, value: value || '' } });
  };

  // CardInfoInputElement는 input Element를 한번 추상화한 컴포넌트입니다.
  // 내부적으로 Error를 판단해 UI로 표현해줍니다.
  return (
    <CardInfoInputElement
      type={type}
      value={value ?? ''}
      className="input-basic"
      ref={setRef.bind(cardNumber)}
      error={{ isError }}
      onChange={onInputChange}
      onFocus={handleCardNumberInputFocus}
    />
  );
});
```

### Auto focusing 기능 구현

아래는 AutoFocus를 위해 만든 custom Hook입니다.

cardStore가 갱신될 때마다 실행됩니다.

```typescript
// auto focusing 기능을 책임지는 custom hook

// 현재 active인 ref의 state가 isValid인 것을 확인하고 처음부터 끝까지 valid를 확인하고 처음 invalid한 곳을 바라보게한다.
export function useSequentialAutoFocus(cardStore?: TCardState[][] | null) {
  useEffect(() => {
    if (!cardStore) return;

    const activeState = findActiveState(cardStore);
    if (activeState && !activeState.errorMessage && activeState.isAllowToFocusNext()) {
      findInvalidStoreAndFocus(cardStore);
    }
  }, [cardStore]);
}

// 현재 active한 Element를 찾는다.
export function findActiveState(cardStore: TCardState[][]): TCardState | null {
  let activeState: TCardState | null = null;

  cardStore.some((cardStateList) =>
    cardStateList.some((cardState) => {
      const isActiveElement = cardState.ref === document.activeElement;
      if (isActiveElement) activeState = cardState;
      return isActiveElement;
    })
  );

  return activeState;
}

// value가 없거나 errorMessage가 있는 state를 focus한다.
export function findInvalidStoreAndFocus(cardStore: TCardState[][]): TCardState | null {
  let invalidState: TCardState | null = null;

  cardStore.some((cardStateList) =>
    cardStateList.some((cardState) => {
      const isInvalid = !cardState.value || !!cardState.errorMessage;
      if (isInvalid) {
        invalidState = cardState;
        // Focus를 이곳에서 실행합니다!!
        cardState.ref?.focus();
      }
      return isInvalid;
    })
  );

  return invalidState;
}
```

## 여러겹의 추상화 계층으로 재사용성을 높여보자.

OOP에선 여러층의 상속계층으로 객체의 재사용성을 높일 수 있습니다.

함수의 세계에선 추상함수를 구체 함수에 포함시키는 방법으로 여러 layer를 두어 재사용성을 높일 수 있습니다.

그 예시로 외부 storage와 소통을 위한 useFetch와 service 객체가 있습니다.

### useFetch와 service 객체 구현

아래는 저의 구현입니다. 단계별로 구현됩니다.

1단계: General Type을 통해 그 어떤 Service든 받아들일 수 있는 useFetch hook

```typescript
import { useCallback, useEffect, useState } from 'react';

export interface Service<T> {
  get(): Promise<T | null>;
  post(newStore?: T | null): Promise<T | null>;
}

// Generic Type을 2가지 경우의 수로 setting할 수 있도록 제공
// 스스로 externalStore를 GET하고 POST해줄 수 있는 객체 넣고, 그 결과를 React lifecycle에 맞춰서 제공해줄 수 있는 hook
export function useFetch<T>(service?: Service<T>): {
  fetchedData?: T | null;
  fetch: (key: keyof Service<T>, newStore?: T | null) => void;
};
export function useFetch<T, K extends { [method: string | number | symbol]: Promise<T | null> }>(
  service?: Service<T> & K
): { fetchedData?: T | null; fetch: (key: keyof Service<T> & keyof K, newStore?: T | null) => void } {
  const [fetchedData, setFetchedData] = useState<T | null | undefined>();

  useEffect(() => {
    service?.get().then((res) => {
      setFetchedData(res);
    });
  }, [service]);

  const fetch = useCallback(
    (key: keyof Service<T> & keyof K, newStore?: T | null) => {
      service?.[key](newStore).then((res) => {
        setFetchedData(res);
      });
    },
    [service]
  );

  return { fetchedData, fetch };
}
```

2단계: 외부 storage와 연결해주는 service 객체 생성

```typescript
const LOCAL_STORAGE_CARD_LIST_KEY = 'cardList';

const get = async () => {
  const item = window.localStorage.getItem(LOCAL_STORAGE_CARD_LIST_KEY);
  return item ? (JSON.parse(item) as TCardList) : null;
};

export const localStorageService: TCardListService = {
  get,
  post: async (cardList: TCardList) => {
    window.localStorage.setItem(LOCAL_STORAGE_CARD_LIST_KEY, JSON.stringify(cardList));
    return get();
  },
};
```

3단계: useFetch를 이용해 cardStore만을 다루는 useFetchCardList hook 생성

```typescript
export function useFetchCardList() {
  const appContext = useApplicationContext();
  const { fetch, fetchedData } = useFetch<TCardList | null>(localStorageService);

  const postCard = useCallback(
    (card: TCard, givenCardId?: string) => {
      const cardId = givenCardId || new Date().getTime();

      if (!fetchedData) {
        fetch('post', { [cardId]: card });
        return;
      }

      fetchedData[cardId] = card;
      fetch('post', fetchedData);
    },
    [fetch, fetchedData]
  );

  const deleteCard = useCallback(
    (cardId: string) => {
      if (!fetchedData) return;

      delete fetchedData[cardId];
      fetch('post', fetchedData);
    },
    [fetch, fetchedData]
  );

  return { cardList: fetchedData, postCard, deleteCard };
}
```

# 페이먼츠 리뷰

구현을 마치고 PR을 올리면 리뷰어님이 제 구현 코드를 보고 리뷰를 주십니다.

(수십개가 넘는 Comment로 두들겨 맞은건 안비밀 😇)

현재 코드에서 좀 더 확장성 있고 읽기 쉬운 코드가 되기 위해 놓친 부분들을 많이 지적해 주셨습니다.

(리뷰해주셔서 진심으로 감사합니다! 준일님! 😁 같이해서 정말 즐거웠고, 덕분에 정말 많이 배웠어요! 👏)

## Element와 동기화 된 객체의 문제점 -> DOM 의존성 문제, 로직 책임 문제

### DOM에 직접적인 의존성을 가지고 있음

state가 DOM을 직접 가지고 있으면, View가 DOM이 아닌 다른 것으로 교체 됐을 때, 바로 문제가 발생할 수 밖에 없다.

### 로직의 사용처와 선언부가 너무 떨어져 있는 것을 미루어볼 때, 책임이 잘못 배정돼있음

로직을 이해하기가 쉽지가 않다.

UI 이벤트 발생에 의해 실행되는 로직인데, 이 로직을 객체가 가지고있다.

컴포넌트를 이용하기 위해 매번 복잡한 객체를 만들어 제공해줘야한다.

나중에 로직을 수정하게 되면, 굉장히 번거로울 것이다.

TODO: jay.bee와 so.so의 repo 주소 넣어주기

### 이렇게 수정!

요래요래 수정

## 여러겹의 추상화 -> 굳이 이렇게까지? 가독성이 크게 떨어지고 코드 수정에 불리

추상화 레이어가 너무 많아 복잡하다.

재사용되지 않는 레이어가 많은데 굳이 이렇게까지 레이어를 쪼개니 이해하기가 쉽지 않다.

YAGNI의 원칙!!

TODO: 관련 posting 주소 넣어주기 (overreacted, 상속지옥, 마틴옹의 문구 넣기)

### 의존성 다이어그램

의존성 다이어그램을 그려보면 복잡한 구조가 한 눈에 보인다는 준일님의 조언에 따라 다이어그램을 그려보았고, 복잡한 의존 관계를 확인하고 줄이기 위해 노력했다.

### 이렇게 수정!

위의 내용을 바탕으로 아래와같이 수정했다.

다이어그램을 그리면서 1번, 내 repo로 가져와서 다시 한번 수정했다.

요래요래 수정

# 너와 나와 우리를 위한 클린코드
**
> 내 코드를 즐겁게 다루기 위한 **지금의 너**와 **미래의 나**와 **우리 팀**을 위한 **클린코드**.

앞서 제목으로 너와 나와 우리를 위한 클린코드라는 제목으로 이 포스트를 시작했습니다.

코드를 계속해서 수정해보면서 느꼈던 클린코드의 중요성을 한줄의 제목으로 표현해봤습니다. 😁

-> 보편적인 클론코드 이론들이 어떻게 유용한지 위에 적은 경험이외의 경험과 이어서 간단히 설명

준일님과의 리뷰가 있은 후에 클린코드에 대한 post들과 클린코드 책(written by Robert C. Martin)을 읽으면서 클린코드에 대해 다시 생각해보았습니다.

몇가지 느꼈던 점을 정리해보자면,

스스로 명확한 원칙이 있어야 상대방도 명확하게 이해할 수 있다.

ㅁㄴㅇㄹㅁㄴㅇㄹ 설명....

코드는 함께 보는 것이다.

ㅁㄴㅇㄹㅁㄴㅇㄹㅁㄴㅇㄹ 설명...

감사합니다.
