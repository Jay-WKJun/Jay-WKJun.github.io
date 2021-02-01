---
layout: post
title: Design Pattern in JS
tags: [DesignPattern, HowToCoding, ProgrammingCommon, Javascript]
excerpt_separator: <!--more-->
---

# Design Pattern

디자인 패턴은 프로그램을 짜기 위한 방법론 이다.

<!--more-->

몇가지 주목할만한 것을 써놓았다.

## Design Pattern Assertion

좋은 Design Pattern을 평가하는 몇가지 특성이 있다.

1. Proven: 검증된

  - THEY PROVIDE SOLID APPROACHES
    TO SOLVING ISSUES IN SOFTWARE DEVELOPMENT USING **PROVEN** TECHNIQUES
    THAT REFLECT THE EXPERIENCE AND INSIGHTS THE DEVELOPERS
    THAT HELPED DEFINE THEM BRING TO THE PATTERN.

  2. Reusable: 재사용 가능

  - A PATTERN USUALLY REFLECTS AN OUT OF THE BOX SOLUTION
    THAT CAN BE ADAPTED TO SUIT OUR OWN NEEDS.

  3. Expressive: 표현적..

  - WHEN WE LOOK AT A PATTERN
    THERE’S GENERALLY A SET STRUCTURE AND VOCABULARY TO THE SOLUTION PRESENTED
    THAT CAN HELP EXPRESS RATHER LARGE SOLUTIONS QUITE ELEGANTLY.

  - PATTERNS ARE NOT AN EXACT SOLUTION.
    IT IS IMPORTANT THAT WE REMEMBER THE ROLE OF A PATTERN IS MERELY
    TO PROVIDE US WITH A SOLUTION SCHEME.

## SOLID

객체지향 프로그래밍을 위한 5가지 원칙

S (SRP)	
단일 책임 원칙 (Single responsibility principle) - 
한 클래스는 하나의 책임만 가져야 한다.

O (OCP)	
개방-폐쇄 원칙 (Open/closed principle) - 
“소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다.”

L (LSP)	
리스코프 치환 원칙 (Liskov substitution principle) - 
“프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다.” 계약에 의한 설계를 참고하라.

I (ISP)	
인터페이스 분리 원칙 (Interface segregation principle) - 
“특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나보다 낫다.”

D (DIP)	
의존관계 역전 원칙 (Dependency inversion principle) - 
프로그래머는 “추상화에 의존해야지, 구체화에 의존하면 안된다.” 의존성 주입은 이 원칙을 따르는 방법 중 하나다.

<b>5가지 원칙에 따라 내가 만든 Module을 평가 할 수 있다.</b>

독립적이어서 재사용성이 뛰어난지, 추상적이어서 범용성이 뛰어난지, 개방과 폐쇄가 적절히 되어 있어 Module 자체나 다른 Module을 침해하진 않는지 고려하여 평가하자

## Rxjs``

Publish & Subscribe과 대응하는 디자인패턴

Observables을 기반으로 하고 Angular에서만 Major함 (대체로 minor)

# Javascript Module 제작시 반드시 고려해야할 것

자바스크립트는 일반적으로 브라우저에서 사용자에게 직접 노출되기 때문에
  기본적으로 보안에 취약할 수 밖에 없는 태생적 특징이 있다.
  그래서 우리는 이런 취약점을 늘 염두하고 작업해야 한다.

  Let's Think!) 왜 글로벌에 변수를 선언하는 것은 피해야 할 습관일까? 그렇다면 그 어떤 상황에서도 글로벌은 이용하면 안될까?

  글로벌에 노출되지 않게 하는 방법엔 어떤 것들이 있을까?

```javascript
// 대표적인 Capsulation
// "IIFE" (즉시 실행 함수)
(function () {
  function createBankAccount(password) {
    let balance = 0;
    return {
      deposit: (amount) => {
        balance += amount;
      },
      withdraw: (amount, pw) => {
        if (password !== pw) {
          return;
        }
        balance -= amount;
      },
    };
  }
  const account1 = createBankAccount("ken", 123);
  const account2 = createBankAccount("wan", 456);
})();
```

# Publish & Subscribe

큰 규모의 Reactive한 Service에서 주로 활용한다.

User가 Subscribe한 주제를 어떤 사람이 Publish하면 연계되어 알림을 주거나 하여 Reactive하게 Service가 가능하다.

MVC Architecture - Obsolete...

Flux, React/Redux - New