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

![componentLifeCycleTotal]({{ "/assets/img/aboutReact/componentLifeCycleTotal.PNG" | relative_url }})

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

![componentLifeCycleAfter16]({{ "/assets/img/aboutReact/componentLifeCycleAfter16.PNG" | relative_url }})

## v.16.3 이전

![componentLifeCycleBefore16]({{ "/assets/img/aboutReact/componentLifeCycleBefore16.PNG" | relative_url }})

# Creation

![componentLifeCycleCreation]({{ "/assets/img/aboutReact/componentLifeCycleCreation.PNG" | relative_url }})

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

![componentLifeCycleUpdate]({{ "/assets/img/aboutReact/componentLifeCycleUpdate.PNG" | relative_url }})

props가 update될 때만 getDerivedStateFromProps()가 발동된다. 그 후부터는 setState()되었을 때랑 동일하다.

## shouldComponentUpate(nextProps, nextState)

이 method만의 특징!!

<u><b>May cancel updating process!!</b></u>

즉, React가 변화된 것을 감지해도 re-render를 하게 할 것이냐 멈추게 할 것이냐를 이곳에서 return하여 결정할 수 있다.

적절히 막으면 App의 성능을 향상시킬 수 있다.

(하지만 잘못 막으면 component를 망가뜨릴 수도 있다.)

## render()

그리고 Update Child Component props까지

## getSnapshotBeforeUpdate(prevProps, prevState)

이전의 props과 state를 input으로 받아 snapshot object를 자유롭게 configure하여 return한다.

DOM이 업데이트 직전에 호출된다. (이 라이프 사이클은 많이 필요하지 않지만, 렌더링되는 동안 수동으로 스크롤 위치를 유지해야할 때와 같은 경우에는 유용할 수 있다)

## componentDidUpdate()

컴포넌트 업데이트 직전에서 호출되는 메소드다. 새로운 props 또는 state가 반영되기 직전 새로운 값들을 받는다.

create때와 마찬가지로 여러 side-effect 요청들을 모두 여기서 할 수 있다.

마찬가지로 this.setState()를 sync하게 사용하면 무한 루프가 일어나게 되므로 사용하면 안된다.

# useEffect()

functional component의 hook으로 class-based component의 hook의 componentDidUpdate(), componentDidMount(), componentWillUnmount()와 대응한다.

## control

- render가 발생할 때 마다(componentDidMount: 초기, componentDUpdate: 매번) effect가 실행된다.

- 한 component에 여러개의 useEffect()를 정의할 수 있다.(발동 조건에 따라 여러 useEffect를 정의해 동시에 사용할 수 있다.)

- 두번째 pram에 Array를 넣어 안에 있는 data가 변경시에만 useEffect()가 발동하도록 할 수 있다. (빈 배열을 넣으면 실행시 1번, unmount시에 1번만 실행된다.)

- effect 함수의 return 값이 있는 경우 hook의 cleanup 함수로 인식하고 다음 effect가 실행되기 전에 실행된다.  (componentWillUnmount는 컴포넌트가 사라지기 전에 한 번만 실행했지만, cleanup 함수는 새로운 effect 실행 전에 매번 호출된다는 차이가 있다.)

```javascript
import React, { useEffect } from 'react'

const testFunc = props => {
    //기동 가장 처음에는 반드시 한번은 발동된다.
    //!!! 두번째 pram에 Array를 넣어 안에 있는 data가 변경시에만 useEffect()가 발동하도록 할 수 있다.
    useEffect(() => {
        console.log("[testFunc]'s useEffect...")
        alert("[testFunc]'s useEffect...");

        //componentWillUnmount()에 대응
        return () => {
            console.log("[testFunc]'s clean up work in useEffect()")
        };

        //props의 persons가 변경되었을 때만 useEffect()가 발동
    }, [props.persons])

    //useEffect는 여러개를 정의할 수 있어 발동조건에 따라 여러 useEffect를 한번에 사용할 수 있다.
    useEffect(() => {
        console.log("2nd [testFunc]'s useEffect...")
    })
}
```

# Optimazation (Automatically)

component 성능 최적화

class-based component hook - shouldComponentUpdate()

## React.memo()

functional component hook - React.memo()

shouldComponentUpdate()는 직접 조건을 설정해줘야하지만 React.memo()는 자동이다.

모든 props와 state의 변화를 감지해 re-render 실행 여부를 판단한다.

```javascript
//shouldComponentUpdate()처럼 props나 state가 변할 시에만 re-render(useEffect()실행)하도록 할 수 있다.
export default React.memo(testFunc);
```

## PureComponent

React.memo()처럼 class-based component에서도 자동으로 확인해주는 것이 있다.

모든 props와 state의 변화를 감지해 re-render 실행 여부를 판단한다.

```javascript
import React, { Purecomponent } from 'react'

//extends Component -> extends Purecomponent
class TestCls extends Purecomponent {
    .......
}
```