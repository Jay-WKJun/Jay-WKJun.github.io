---
layout: post
title: JavaScript
tags: [JavaScript, Theories]
excerpt_separator: <!--more-->
---

# JavaScript

프로토타입기반의 객체지향 프로그래밍 언어로, 엔진은 인터프리터, 스크립트 언어에 해당된다. 

특수한 목적이 아닌 이상 모든 웹 브라우저에 인터프리터가 내장되어 있다. 오늘날 HTML, CSS와 함께 웹을 구성하는 요소 중 하나다. 

<!--more-->

HTML이 웹 페이지의 기본 구조를 담당하고, CSS가 디자인을 담당한다면 JavaScript는 클라이언트 단에서 웹 페이지가 동작하는 것을 담당한다. 웹 페이지를 자동차에 비유하자면, HTML은 자동차의 뼈대, CSS는 자동차의 외관, JavaScript는 자동차의 동력이라고 볼 수 있다.

# JavaScript 구동 원리

가장 성능이 좋고, 가장 많이 쓰이는 크롬 V8 엔진을 기반으로 작성!

싱글 쓰레드 기반, 콜백 큐 사용

![ChromeV8Engine]({{ "/assets/img/aboutJavaScript/javaScriptEngine.png" | relative_url }})

Parser : Lexical Analysis(코드의 의미를 이해하기 위해 token이라는 작은 단위들로 코드를 쪼개는 일)를 진행한다. 작성한 High-level 코드가 Javascript Engine 안에서 Parser를 통해 syntax와 semantics가 맞는지 확인하고, 틀리면 error가 뜬다.

AST : Abstract Syntax Tree의 약자로, parser에서 분해된 token들을 기반으로 나무 구조를 만든다.(ex. DOM Tree, CSSOM Tree가 구성되는 것과 비슷한 개념이다.)

[AST Explorer](https://astexplorer.net/) : 코드가 어떻게 나무 구조로 변경되는지를 시각적으로 볼 수 있는 사이트.

AST에서 생성된 나무 구조는 인터프리터와 컴파일러를 거쳐서 컴퓨터가 이해할 수 있는 Bytecode와 Optimized Code로 변환된다.

자바스크립트 엔진의 구조와 구성 원리를 이해하면, 반대로 코드를 작성할 때 어떻게 Optimized Code, 효율적으로 최적화된 코드를 작성할 수 있는지를 알 수 있다.

## JIT Compiler (Just In Time)

V8 Engine만의 독특한 방식으로 다른 engine보다 빠른 이유이다.

1. AST를 통해 나무 구조로 변환된 코드는 최초에 인터프리터에게 전달된다.(참고로 V8엔진에서 인터프리터의 명칭은 Ignition이다.) 인터프리터는 빠르게 코드를 Bytecode로 변환한다.

2. 인터프리터가 코드를 실시간으로 변환하면서 브라우저에게 특정 작업을 지시하는 동안, 프로파일러(Profiler)는 입력받은 코드에서 최적화할 수 있는 부분을 찾아서 기록해놓는다.

3. 최적화가 가능한 부분을 찾으면 프로파일러는 이를 컴파일러에게 전달하고, 컴파일러는 인터프리터에 의해 실시간으로 웹사이트가 구동되는 동안 필요한 부분을 기계어로 변환하여 최적화를 진행한다.

4. 최적화한 코드를 수행할 차례가 다가오면, Bytecode 대신 컴파일러가 변환한 최적화된 코드가 그 자리를 대체하여 실행된다.

하지만 Compiler도 완벽하지는 않기 때문에 문제가 발생할 수 있다.

# Interpreter vs Compiler

고차원 언어를 기계가 이해할 수 있는 저차원 언어로 변환하는 방식은 크게 2가지 방식이 있다. ① 인터프리터를 이용한 변환과 ② 컴파일러를 이용한 변환이다.

1. 인터프리터 : 코드를 한 줄 한 줄 읽어내려가며 한 줄씩 Bytecode로 변환한다.

- 장점 :코드 전체가 컴파일 된 Compilation이 완성되는 것을 기다릴 필요 없이, 한 줄 한 줄 변환하기 때문에 실행 속도가 빠르다.

- 단점 : 자바스크립트 코드가 복잡해질수록 점점 속도가 느려진다. 예를 들어, 같은 코드를 여러차례 반복하는 반복문의 경우, 같은 결과를 반복하는 것임에도 불구하고 코드를 한 줄 한 줄 읽는 방식 때문에 10억번을 반복해야 한다면 말 그대로 10억번을 반복해서 코드 변환을 진행하게 된다.

2. 컴파일러 : 한 줄 한 줄 번역하지 않고 파일 전체를 읽은 뒤, 코드의 의미를 해석하고 파일 전체를 기계어로 컴파일해서 변환한다.

- 장점 : 컴파일러는 작업을 단순화시킨다. 예를 들어 특정 함수를 10억번 반복해야 할 경우, 컴파일 과정에서 함수를 반복하는 것이 아니라 함수의 결과물을 반복하도록 컴파일 한다. 이처럼 불필요한 동작을 제거하는 컴파일러의 방식을 최적화, optimization이라고 한다. (인터프리터는 optimize하지 않는다.)

- 단점 : 코드를 바로 실행하지 않고, 코드 실행 전 전체를 컴파일 하는 과정이 필요하기 때문에 초기에 속도가 느릴 수 밖에 없다.