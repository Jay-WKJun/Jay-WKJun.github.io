---
layout: post
title: React Inside
tags: [react, javascript, virtualDOM]
excerpt_separator: <!--more-->
---

# React Inside

React 내부 원리와 주의해야 할 점들 모음

<!--more-->

# virtual DOM

React의 가장 큰 특징으로 DOM에 변화를 줄 시 Virtual DOM을 render하고 Real DOM과 차이점을 비교해 변한 곳만 갱신한다.

![HowReactUpdateDOM]({{ "/assets/img/aboutReact/HowReactUpdateDOM.PNG" | relative_url }})

# Redering Adjacent JSX Elements

최상위 JSX tag를 나란히 하면 에러이기 때문에 이를 위한 여러가지 방법이 있다.

- Array

단점은 각 tag 들에 key를 필요로 한다는 점이다.

- Aux.js

children을 바로 return 하는 외부 파일을 통해 나란히 나열된 여러 tag들을 한번에 return 할 수 있도록 해준다.

중간에 div 등을 끼우고 attribute를 받아 사용하는것도 가능하다.

```javascript
//Aux.js
const aux = props => props.children

export default aux;
```

```javascript
render(){
    return(
        <Aux>
            <p>adsf</p>
            <div>qwer</div>
            <input>zxcv</input>
        </Aux>
    )
}
```

- React.Fragment

React에서 제공하는 것을 사용한다.

결과는 위와 동일하다.

```javascript
render(){
    return(
        <React.Fragment>
            <p>adsf</p>
            <div>qwer</div>
            <input>zxcv</input>
        </React.Fragment>
    )
}
```

- HOC(Higher Order Component)

```javascript
//WithClass.js
import React from 'react'

const withClass = (WrappedComponent, className) => {
    return props => {
        //만약 props={props} 로 해버릴 경우 사용할 때 props.props.()으로 되어 props의 props가 되어버리므로 {...props}를 통해 props 자체를 통째로 바꿔버린다.
        <div className={className}>
            <WrappedComponent {...props} />
        </div>
    }
}
export default withClass;
```

```javascript
//App.js
export default withClass(App, className)
```

# Setting State Correctly

setState({})로 state를 갱신시에 <u><b>반드시 주의</b></u>해야 할 점

```javascript
//잘못된 setting 방법
//타이핑으로 발동되는 함수라고 가정
this.setState({
    persons: persons,
    //Caution!! : this.state를 그대로 사용한게 잘못!!
    changeCounter: this.state.changeCounter + 1
});
```

여기서 주의해야할 점은

this.setState()는 re-render과정에서 바로 실행되는 것이 아니라 일단 Scheduled 되었다가 나중에 여유가 있을 때 실행이 된다.

따라서 화면이 갱신된 만큼 this.state.changeCounter가 갱신되었다고 확신할 수 없고 항상 최신상태라고 확신할 수 없다.

(App의 덩치가 커지면 이런 일이 더 많이 발생한다.)

```javascript
//올바른 setting 방법
//타이핑으로 발동되는 함수라고 가정
//setState는 Object뿐만 아니라 function도 받아들인다.
this.setState((prevState, props) => {
    return {
    persons: persons,
    changeCounter: prevState.changeCounter + 1
}
});
```

이렇게 하면 반드시 갱신되지 않은 이전 상태의 state를 받아 갱신 할 수 있다.

# check PropTypes

올바른 prop type이 받아졌는가를 확인하고 제한할 수 있다.

library나 package를 만들어 배포하거나 협업할때 유용하다.

```script
npm install --save prop-types
```

```javascript
import React, { Component } from 'react';
import Aux from '../../../hoc/Aux';
import Proptypes from 'prop-types';

class App extends Component {
    render(){
        return (
            <Aux>
                <p onClick={this.props.click}>
                    I'm {this.props.name} and I am {this.props.age} years old!
                </p>
            </Aux>
        )
    }
}

//prop의 type들을 설정한다.
//{propName : Types}
App.propTypes = {
    click: Proptypes.func,
    name: Proptypes.string,
    age: Proptypes.number
}
```

혹시라도 잘못된 type의 prop을 받으면 error message를 내보낸다.

# refs

JS에선 document.querySelector("input") 등으로 DOM의 요소를 특정한다.

React에선 ref라는 attribute를 적용하여 특정 DOM요소를 특정할 수 있도록 해준다.

## class-based Component

2가지 적용 방법이 있다.

```javascript
import React, { Component } from 'react';
import Aux from '../../../hoc/Aux';

class App extends Component {
    constructor(props){
        super(props);
        //***방법2
        //우선 constructor에서 ref를 받아 저장한다.
        this.inputElement2 = React.createRef();
    }
    componentDidMount(){
        //render된 DOM 전체를 탐색해 가장 첫번째 것을 고른다.
        //document.querySelector('input').focus();

        //***방법 1
        //아래에서 생성한 ref property를 이용하면 내가 지정해준 DOM요소를 특정할 수 있다.
        this.inputElement.focus();

        //***방법 2
        //저장된 ref는 여러 DOM요소에 사용할 수 있기 때문에 여러가지중 하나를 특정할 수 있도록 current를 붙여줘야한다.
        this.inputElement2.current.focus();
    };

    render(){
        return (
            <Aux>
                <p onClick={this.props.click}>
                    I am {this.props.name} and I am {this.props.age} years old! </p>
                <input type="text"></input>

                <input ref={(inputEl) => {
                    //***방법 1
                    //param은 react가 만들어준 ref이름이 들어온다.
                    //this.anything 해서 class에 anything property가 생성된다.
                    this.inputElement = inputEl
                    }}
                    type="text" />
                

                <input 
                ref = {
                    //***방법 2
                    //constructor에서 생성한 ref를 이 요소에 적용한다.
                    this.inputElement2
                    }
                type="text" />
            </Aux>
        )
    }
}
export default App;
```

## functional Component

```javascript
import React, { useRef } from 'react';
const App = (props) => {
    //class의 React.createRef()와 같은 역할이다.
    //사용법은 class와 같다.
    const toggleButtonRef = useRef();
}
```

# Context

최상위 layer에서 처리한 정보가 최하단 layer에 필요할때 여러 layer를 거치게 되는데 이는 매우 비효율적이다.

따라서 <b>최상위 layer와 최하단 layer에서만 global하게 사용 가능</b>하도록 하는 기능이 React의 Context이다.

## v.16.6 이후

v.16.6 부터 context에 접근 할 수 있는 새로운 기능이 생겼다.

```javascript 
//context.js
import React from 'react';

//()안에 context초기화 필요! Type은 자유!
const authContext = React.createContext({
    isAuthenticated: false,
    login: () => {}
});

export default authContext;
```

```javascript
//App.js
import React, { Component } from 'react';
import Last from './last'
import AuthContext from './context';
class App extends Component {
    state = {
        isAuthenticated: false
    }

    loginHandler = () => {
        setState({
            isAuthenticated: true
        })
    }

    render() {
        return (
            //이렇게 감싸면 안에 있는 자식 component들은 모두 해당 context에 접근 할 수 있게 된다.
            //value를 통해 context를 초기화한다.
            <AuthContext.Provider
                value={{
                    isAuthenticated: false,
                    login: this.loginHandler
                }}
                >
            <Last>
            </Last>
            </AuthContext.Provider>
            
        )
    }
}
```

```javascript
//last.js
import AuthContext from './context';
class Last extends Component {

    //반드시 static!
    //이렇게 간편하게 Context 내부에 접근할 수 있다.
    static contextType = AuthContext;
    /*이렇게 접근하면 좋은 점: 외부 API와도 context 데이터로 통신이 가능하다.*/

    render(){
        return(
            //prop들이 몇개의 layer를 거쳐와야 했지만 context를 통해 한방에 접근이 가능해졌다.
            {
                this.contextType.isAuthenticated ? <p> Log in!! </p> : null
            }
            <button onClick={this.contextType.loginHandler}></button>
        )
    }
}
export default Last;
```

functional component의 경우엔...?

useContext()

```javascript
import React, { useContext } from 'react';
import AuthContext from './context';

const Last = () => {
    //이걸로 context에 접근 가능 (aka. context hook)
    const authContext = useContext(AuthContext);

    return (
        //prop들이 몇개의 layer를 거쳐와야 했지만 context를 통해 한방에 접근이 가능해졌다.
        {
            authContext.isAuthenticated ? <p> Log in!! </p> : null
        }
        <button onClick={authContext.loginHandler}></button>
    )

}
```

## v.16.6 이전

Provider를 이용해 초기화 하는 방법은 동일하다.

다만 consumer의 방법이 다르다.

```javascript
//last.js
import AuthContext from './context';
class Last extends Component {

    render(){
        return(
            //(context)를 통해 context의 내부에 접근할 수 있다.
            //Consumer의 안은 반드시 함수여야 한다.
            <AuthContext.Consumer>
            {
                (context) => context.isAuthenticated ? <p> Log in!! </p> : null
                <button onClick={context.loginHandler}></button>
            }
            </AuthContext.Consumer>
            //이렇게 부분적으로 사용하는 것도 가능하다.
            <p>Don't Use Context</p>
        )
    }
}
export default Last;
```