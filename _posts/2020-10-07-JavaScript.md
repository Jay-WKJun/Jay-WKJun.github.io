---
layout: post
title: JavaScript
tags: [JavaScript, Theories]
excerpt_separator: <!--more-->
---

# JavaScript

프로토타입기반의 객체지향 프로그래밍 언어로, 엔진은 인터프리터, 스크립트 언어에 해당된다. 

특수한 목적이 아닌 이상 모든 웹 브라우저에 인터프리터가 내장되어 있다. 오늘날 HTML, CSS와 함께 웹을 구성하는 요소 중 하나다. 

HTML이 웹 페이지의 기본 구조를 담당하고, CSS가 디자인을 담당한다면 JavaScript는 클라이언트 단에서 웹 페이지가 동작하는 것을 담당한다. 웹 페이지를 자동차에 비유하자면, HTML은 자동차의 뼈대, CSS는 자동차의 외관, JavaScript는 자동차의 동력이라고 볼 수 있다.

<!--more-->

# JavaScript 구동 원리

가장 성능이 좋고, 가장 많이 쓰이는 크롬 V8 엔진을 기반으로 작성!

![ChromeV8Engine]({{ "/assets/img/aboutJavaScript/javaScriptEngine.png" | relative_url }})

Parser : Lexical Analysis(코드의 의미를 이해하기 위해 token이라는 작은 단위들로 코드를 쪼개는 일)를 진행한다.

AST : Abstract Syntax Tree의 약자로, parser에서 분해된 token들을 기반으로 나무 구조를 만든다.(ex. DOM Tree, CSSOM Tree가 구성되는 것과 비슷한 개념이다.)

[AST Explorer](https://astexplorer.net/) : 코드가 어떻게 나무 구조로 변경되는지를 시각적으로 볼 수 있는 사이트.

AST에서 생성된 나무 구조는 인터프리터와 컴파일러를 거쳐서 컴퓨터가 이해할 수 있는 Bytecode와 Optimized Code로 변환된다.

자바스크립트 엔진의 구조와 구성 원리를 이해하면, 반대로 코드를 작성할 때 어떻게 Optimized Code, 효율적으로 최적화된 코드를 작성할 수 있는지를 알 수 있다.

# Interpreter vs Compiler

고차원 언어를 기계가 이해할 수 있는 저차원 언어로 변환하는 방식은 크게 2가지 방식이 있다. ① 인터프리터를 이용한 변환과 ② 컴파일러를 이용한 변환이다.

1. 인터프리터 : 코드를 한 줄 한 줄 읽어내려가며 한 줄씩 Bytecode로 변환한다.

- 장점 :코드 전체가 컴파일 된 Compilation이 완성되는 것을 기다릴 필요 없이, 한 줄 한 줄 변환하기 때문에 실행 속도가 빠르다.

- 단점 : 자바스크립트 코드가 복잡해질수록 점점 속도가 느려진다. 예를 들어, 같은 코드를 여러차례 반복하는 반복문의 경우, 같은 결과를 반복하는 것임에도 불구하고 코드를 한 줄 한 줄 읽는 방식 때문에 10억번을 반복해야 한다면 말 그대로 10억번을 반복해서 코드 변환을 진행하게 된다.

2. 컴파일러 : 한 줄 한 줄 번역하지 않고 파일 전체를 읽은 뒤, 코드의 의미를 해석하고 파일 전체를 기계어로 컴파일해서 변환한다.