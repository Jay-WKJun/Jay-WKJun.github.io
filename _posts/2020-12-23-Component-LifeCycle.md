---
layout: post
title: React Component LifeCycle
tags: [react, javascript, component, lifecycle]
excerpt_separator: <!--more-->
---

# React Component Lifecycle

React의 Component들에는 특정 LifeCylcle이 존재한다.

생명주기란 컴포넌트가 생성되고 사용되고 소멸될 때 까지 일련의 과정을 말한다.

<!--more-->

이러한 생명주기 안에서는 특정 시점에 자동으로 호출되는 메서드가 있는데, 이를 영어론 <b>LifeCycle Hooks</b> 혹은 <b>라이프 사이클 이벤트</b>라고 한다.

![componentLifeCycleTotal]({{ "/assets/img/aboutReact/componentLifeCycleTotal.png" | relative_url }})

ad. v.16이후로 functional component에서 useState를 이용한 state이용이 가능해졌지만 아직까지 LifeCycle Hooks은 이용할 수 없다.<u>(only Class-based component)</u>

ref.

https://velog.io/@kyusung/%EB%A6%AC%EC%95%A1%ED%8A%B8-%EA%B5%90%EA%B3%BC%EC%84%9C-%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8%EC%99%80-%EB%9D%BC%EC%9D%B4%ED%94%84%EC%82%AC%EC%9D%B4%ED%81%B4-%EC%9D%B4%EB%B2%A4%ED%8A%B8#componentdidmount

https://velopert.com/1130

# version

v16.3 이전과 이후로 크게 달라졌다.

## v.16.3 이후

- componentWillMount, componentWillReceiveProps, componentWillUpdate를 v17 부터 사용불가
- componentWillReceiveProps 대체 메서드 추가 getDerivedStateFromProps
- componentWillUpdate 대체 메서드 추가 getSnapshotBeforeUpdate
- componentDidCatch 컴포넌트 에러 핸들링 API 추가

![componentLifeCycleAfter16]({{ "/assets/img/aboutReact/componentLifeCycleAfter16.png" | relative_url }})

## v.16.3 이전

![componentLifeCycleBefore16]({{ "/assets/img/aboutReact/componentLifeCycleBefore16.png" | relative_url }})

# Creation

![componentLifeCycleCreation]({{ "/assets/img/aboutReact/componentLifeCycleCreation.png" | relative_url }})

## constructor(props)

- custom할 경우엔 <b>super(props)</b>를 반드시 해주어야한다.

- state를 초기화 해줄 수 있다.(웬만하면 여기서 해야한다.) (setState()는 아직 state가 없기 때문에 불가능하다.)

- Don't cause side-effect!

side-effect란, 예를 들면

1. HTTP request 보내기
2. local storage 저장
3. Google analytic 보내기 등...

Because! 성능을 떨어뜨리는 원인이 되고, 불필요한 re-render cycle을 유발할 수 있기 때문이다.

## getDerivedStateFromProps(props, state)

컴포넌트가 인스턴스화 된 후, 새 props를 받았을 때 호출된다.

거의 쓰이지 않지만 보통 props가 변해 내부의 state를 바꿔야 할 때 사용하게 된다.

주의할 점으로 setState를 사용하는 것이 아닌 갱신된 state 값을 retrun 해야한다. state를 갱신하는 새로운 state 객체를 반환할 수 있고, 새로운 props가 state 갱신을 필요로 하지 않음을 나타내기 위해 null을 반환할 수도 있다.

## render()

Prepare & Structure my JSX code

## Render Child Components

render()에 포함된 자식 component의 creation lifecycle까지 모두 끝내는 단계

## componentDidMount()

매우 자주쓰이고 중요한 method.

이곳에서 side-effect를 사용할 수 있고 다른 JavaScript 프레임워크를 연동하거나 보통 AJAX나 타이머를 생성하는 코드를 작성한다.

하지만 <u>synchrously하게 state를 update할 수 없다.</u>(async하게는 상관없다.)(update하는 순간 <b>무한루프</b>에 빠지게 된다.)

# Update

![componentLifeCycleUpdate]({{ "/assets/img/aboutReact/componentLifeCycleUpdate.png" | relative_url }})