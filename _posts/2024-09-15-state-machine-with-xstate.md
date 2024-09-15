---
layout: post
title: Finite State Machine (with XState)
tags: [state machine, XState, JavaScript, Web, Frontend, state, state management]
excerpt_separator: <!--more-->
---

# Finite State Machine (with XState)

애플리케이션이 발전하다보면 수많은 state가 생기게 됩니다. 그러다보면 여러 state간의 관계가 생기고 복잡해집니다. 단순히 로직으로 이를 관리하다보면 코드가 복잡해지고 유지보수가 어려워집니다.

상태 하나가 추가되면 다른 상태들과의 관계를 고려해야하고, 그럼 상태 하나가 추가될 때마다 복잡도가 기하급수적으로 증가합니다.

이런 복잡한 문제를 깔끔하게 해결하기 위해, Finite State Machine을 사용할 수 있습니다.

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

예시는 결제 진행으로 크게 1. 결제 준비 2. 결제 진행 단계로 나뉩니다.

결제 준비 단계에선 비회원, 회원 상태를 가지고 있고, 비회원과 회원 상태에 따라 결제 준비 단계가 약간 다르게 진행됩니다.

그리고 결제 진행 단계에선 비동기 처리 상태에 따라 상태와 UI가 변경되고 결제가 완료되면 종료됩니다.

# Finite State Machine(w. XState) 후기

후기...

솔직히 처음 사용할 땐 굉장히 복잡했다. 메소드도 많고, 가능한 property도 많아서 익히는 데 시간이 걸렸다. 이런 학습 곡선은 단점이라고 생각한다.

마치 리덕스처럼 무언가를 하기 위해 코드도 많이 작성해야하는 점도 복잡하고 번거로웠다.

하지만, 한번 익숙해지면 상태 머신을 사용하는 것이 얼마나 편리하고 유지보수가 쉬운지 알 수 있었다.

상태 머신만으로 애플리케이션의 모든 상태를 관리할 수 있을 정도로 강력하다고 생각한다.

따라서, 상태 머신은 익히기는 조금 힘들지만, 그만한 가치는 충분하다고 생각한다.

# Conclusion

XState에선 FSM 구현을 위해 제공하는 메소드와 기능들이 정말 많지만, 아직은 기본적인 기능들만 사용해보았다.

앞으로 계속해서 사용해보며 더 잘 활용해볼 수 있는 방법을 알아봐야겠다.

상황에 따라 적절히 잘 사용하면 어떤 상황에서도 복잡한 상태관리를 깔끔하게 해결할 수 있는 충분히 강력한 도구라고 생각한다.

# ref

- [XState](https://xstate.js.org/docs/)
https://fe-developers.kakaoent.com/2022/220922-make-cart-with-xstate/

https://geekyants.com/blog/introduction-to-state-machines-in-react-with-xstate

https://kyleshevlin.com/guidelines-for-state-machines-and-xstate/

https://dev.to/gtodorov/sustainable-xstate-machines-2065

유한 상태 기계 - 위키피디아 https://ko.wikipedia.org/wiki/%EC%9C%A0%ED%95%9C_%EC%83%81%ED%83%9C_%EA%B8%B0%EA%B3%84
