---
layout: post
title: Exectuion Context
tags: [JavaScript, ExectuionContext]
excerpt_separator: <!--more-->
---

# Execution Context

'실행 문맥'이라고도 한다.

A box, a container, or a wrapper, which stores variables and in which a piece of out code is evaluated and executed.

즉, variables와 평가되고 실행할 코드들의 뭉치들을 말한다.

<!--more-->

Default는 언제나 Global Execution Context이다.

# Execution Context Object

Execution Context는 Object와 연관되어 있고 구성은 다음 그림과 같다.

![ExecutionContextObject]({{ "/assets/img/aboutJavaScript/ExecutionContextObject.png" | relative_url }})

생성단계는 크게 두가지로 나뉘고 순서대로 진행된다.

1. Creation Phase
    - Variable Object(VO) 생성
    - Scope Chain 생성
    - 'this' variable 값 설정
2. Execution Phase
    - 생선된 현재 Execution Context의 코드를 한줄 씩 읽어나간다.

## Variable Object(VO)

3가지 과정이 있고 마지막 두 과정을 'Hoisting'이라고 한다.

- argument Object가 만들어지고 이는 function에 관련된 모든 argument를 포함한다.
- function declaration을 확인하고, 각 function은 function을 가리키는 property를 VO안에 만들게 된다.
- variable declration을 확인하고, 각 variable은 VO에 만들어지고 'undefined'로 세팅된다.

주의 할 점은 function은 실행 페이즈 전에 이미 정의되어 있지만

변수는 undefined로 정의가 되어있다는 차이점이 있다.

## Scope Chain



# Execution context stack

Exectuion context는 정의가 아닌 function을 불럿을 때 stack에 쌓이게 된다.

```javascript

var name = 'John';

function first(){
    var a = 'Hello!';
    //두번째
    second();
    var x = a + name;
    //function에 모든게 진행되면 function은 return하고 stack에서 pop up 된다.
}

function second(){
    var b = 'Hi!';
    //세번째
    third();
    var z = b + name;
}

function third(){
    var c = 'Hey!';
    var z = c + name;
}

//첫번째 Execution context가 stack에 쌓임
first();

```

![ExecutionStack]]({{ "/assets/img/aboutJavaScript/ExecutionStack.png" | relative_url }})