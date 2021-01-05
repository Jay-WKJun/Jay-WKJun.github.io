---
layout: post
title: React 2 jsSyntax in React And Collection, Error
tags: [react, javascript, collection, list, error boundary]
excerpt_separator: <!--more-->
---

# React와 JS Syntax mixing

React를 사용하면서 많은 부분을 기본 JS기능들과 함께 사용 할 수 있다.

DataStructure나 method도 마찬가지이다. 모든 JS가 react에 기초가 된다.

<!--more-->

# JS logic implement

if를 사용하는데 2가지의 방법이 있다.

1. JSX안에 {}를 통해 삼항 연산자 사용
2. JSX에 넣고 싶은 JSX를 변수화해 JS구문 자유롭게 사용

```javascript
//class가 잇다고 가정...   App.js
state = {
    persons:[
        {name: "HHH", age:"40"},
        {name: "book", age:"12"}
    ],
    showOrNot: false
};

toggleParagraph = () => {
    const toggleStatus = this.state.showOrNot;
    //boolean이 거꾸로 저장이 될 것이다.
    this.setState({showOrNot: !toggleStatus});
}

//2. 변수를 활용해 JS로 logic을 만들고 결과를 반환하는 방식
let paragraph = null;
if(this.state.showOrNot) {
    paragraph = <p> This one is for JS!!</p>;
} else{
    paragraph = null;
}

render(){
    //1. 3항 연산자를 통해 보여주고 없새는 방법
    return (
        <div>
            <div>
                <button onClick={this.toggleParagraph}>click This!</button>
                {
                    this.state.showOrNot ?
                    <p>Hi!!!</p>
                    : null
                }
                {paragraph}
            </div>
        </div>
    )
}
```

# Deal with Lists

![ListAndKey]({{ "/assets/img/aboutReact/ListAndKey.PNG" | relative_url }})

중요!!

JSX를 iterate하여 생성한 후, 변경되면 React 특유의 Virtual DOM이라는 것이 변경된 데이터로 그려지고 앞서 그려진 real DOM과 비교를 하여 달라진 부분만을 고치게 되어 굉장히 빠르게 DOM을 수정할 수 있다.

이때, 필요한게 JSX에 key로 같은 key를 가진 JSX를 대조하여 다른것만 바꾸는데 이 key가 없다면 React는 모든 JSX를 처음부터 다시 그려서 넣는다.

따라서 iterate로 그린 JSX에 key property는 중요하다.

# Error Boundary

v.16부터 Error Boundary라는 개념이 생겼다. 일종의 Try Catch같은 것으로 custom하게 error hanlding 할 수 있다.

```javascript
import React, { Component } from "react";

class ErrorBoundary extends Component {
    state = {
        hasError: false,
        errorMessage: ''
    }

    //얘가 catch와 같은 역할을 한다.
    componentDidCatch = (error, info) => {
        this.setState({hasError: true, errorMessage: error})
    }

    render() {
        if(this.state.hasError){
            return <h1>{this.state.errorMessage}</h1>;
        }else{
            //에러가 없다면 children을 내보내 정상적으로 흘러가게 한다.
            return this.props.children
        }
    }
}

export default ErrorBoundary;
```

```javascript
//App.js
import ErrorBoundary from "./ErrorBoundary";

render(){
    return(
        //에러가 없다면 children을 return하기 때문에 ErrorBoundary안의 것들을 정상적으로 출력한다.
        <ErrorBoundary>
            <div>
                <h1>It works!</h1>
            </div>
        </ErrorBoundary>
    )
}
```