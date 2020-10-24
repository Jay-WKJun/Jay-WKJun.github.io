---
layout: post
title: Prototype in JS
tags: [JavaScript, Prototype, theories]
excerpt_separator: <!--more-->
---

# prototype

ECMAScript에서 정의한 프로토타입 개념, “ 다른 객체들과 공유된 속성을 제공하는 객체 ”

객체가 만들어지기 위해 그 객체의 모태가 되는 것, java의 class과 같다고 보면 된다.

객체가 만들어지기 위해서는 자신을 만드는 데 사용된 원형인 프로토타입 객체를 이용하여 객체를 만듭니다.

<!--more-->

```javascript
//함수에서 객체 생성 원리
var o = new Foo();

//빈 객체를 만들고
var o = new Object();
//빈 객체의 prototype을 Foo의 prototype으로 해준 뒤
o.[[Prototype]] = Foo.prototype;
//Foo 객체에 o객체를 접목하여 객체를 만든다.
Foo.call(o);


//Object.create() 함수의 내부 로직, 프로토타입 상속의 원리를 보여준다
function create_object(o){
    function F() {}
    F.prototype = o;
    return new F();
}
```

# Javascript와 Object

JS는 함수기반언어로 함수에서 객체를 생성한다. (다른 언어들 처럼 Class에서 객체를 생성하지 않는다.)

즉, 반드시 함수를 통해서만 객체를 생성할 수 있다.

## 해당 함수에 Contstructor(생성자) 자격 부여

객체를 생성할때에는 Constructor 자격을 부여받아야만 new를 통해 객체를 만들 수 있다.

Constructor(생성자)는 해당 함수의 prototype Object에 의해 권한이 부여됨

이것이 오로지 함수만 new 키워드를 사용할 수 있는 이유이다.

## 내부 구성

JavaScript에서는 함수를 정의하고, 파싱단계에 들어가면, 내부에 별개로 해당 함수 이름과 같은 Prototype Object도 같이 생성이 됩니다.

함수의 숨은 속성인 이 prototype 속성은 Prototype Object를 가리킵니다.

![HowFunctionMadeOf]({{ "/assets/img/aboutJavaScript/WhenDeclareAFunction.png" | relative_url }})

# Prototype Object

Prototype Object는 일반적인 객체와 같으며 기본적인 속성으로 constructor와 __proto__를 가지고 있습니다.

![PrototypeObject]({{ "/assets/img/aboutJavaScript/FunctionsprototypeObject.png" | relative_url }})

constructor는 Prototype Object와 같이 생성되었던 함수를 가리키고 있습니다. 해당 함수를 가리키며 객체 안에 복사해놓고 있습니다. 이것을 이용해 새로운 instance를 만들어냅니다.

__proto__는 Prototype Link입니다.

주목! Prototype Object는 일반적인 객체이므로 속성을 마음대로 추가/삭제 할 수 있습니다. 같은 함수를 통해 만들어진 모든 instance들은 prototype link를 통해 모태의 prototype에 접근할 수 있습니다.

# Prototype Link

모든 객체는 반드시 '\_\_proto\_\_'라고 하는 속성(property)을 가지며 속성안엔 해당 객체를 만들때 사용한 원형인 Prototype Object가 있다.

'\_\_proto\_\_'는 Prototype Link로 이 속성은 객체가 만들어지기 위해 사용된 원형인 프로토타입 객체를 숨은 링크로 참조하는 역할을 합니다.

즉, __proto__는 객체가 생성될 때 조상이었던 함수의 Prototype Object를 가리킵니다.

prototype 속성은 함수만 가지고 있던 것과는 달리 __proto__속성은 모든 객체가 빠짐없이 가지고 있는 속성입니다.

# Prototype inheritance (프로토타입 상속)

![usePrototype]({{ "/assets/img/aboutJavaScript/usePrototype.png" | relative_url }})

위와 같이 작성했을 경우에 드는 의문점은 'Car에 직접 property를 추가한것도 아닌데 어떻게 4라는 값이 출력되는것인가?' 이다

정답은 객체에 있는 '\_\_proto\_\_' property에 의한 Prototype Link와 이를 이용한 Prototype inheritance 덕분이다.

bmw.\_\_proto\_\_는 Car.prototype을 참조하고 있기 때문에 bmw 객체에서 Car.prototype의 property를 사용할 수 있는 것이다.

## Prototype Chain

![object's__Proto__]({{ "/assets/img/aboutJavaScript/objects__Proto__.png" | relative_url }})

bmw는 wheel 속성을 가지고 있지 않기 때문에 wheel 속성을 찾을 때 까지 객체가 생성될 때 조상이었던 함수의 Prototype Object를 탐색한다.

최상위인 Object의 Prototype Object까지 도달했는데도 못찾았을 경우 undefined를 리턴한다. 이렇게 __proto__속성을 통해 상위 프로토타입과 연결되어있는 형태를 프로토타입 체인(Chain)이라고 한다.

![prototypeLink]({{ "/assets/img/aboutJavaScript/prototypeLink.png" | relative_url }})

# Prototype Inheritance와 Constructor Link

custom function끼리 상속관계를 만들게 하기 위한 방법들이 여럿있고

이는 prototype기반 상속이라는 시스템 때문에 생각해봐야될 것들이 있다.

## new 생성자

new를 통한 잘못된 상속

```javascript
function Person(){
    var name = 'something';
    var age = 0;
    var getMyName = function (){
        console.log('My name is ' + this.name);
    }
}

function Jay(){
    var person = new Person();
    person.name = 'jay';
    person.age = 23;
    return person;
}

var me = new Jay();
me.getMyName(); // My name is jay
console.log(me instanceof Jay);     //false!!
//Q. new를 하여 constructor를 발동하는 순간 function이 실행이 되는가? (function의 내용 자체가 생성자 그 자체?)
//      실행되었다면 jay객체에서 person이 반환되고 me는 person객체가 된다.
//Q. function을 토대로 객체를 미리 만들어 놓은 prototype을 복사한게 instance면 function의 내용물들이 실행없이 그대로여야 하는것 아닌가?
console.log(me instanceof Person);  //true
```

new를 통한 일반적인 상속

```javascript
function Person(){
    var name = 'something';
    var age = 0;
    var getMyName = function (){
        console.log('My name is ' + this.name);
    }
}

function Jay(){
    this.name = 'jay';
    this.age = 23;
}

Jay.prototype = new Person();
var me = new Jay();

me.getMyName(); // My name is jay
console.log(me instanceof Jay);     //true
console.log(me instanceof Person);  //true
```

하지만 주의해야할 점은 me의 constructor는 Person을 바라보고 있다는 점이다!

이는 Jay.prototype에 new Person()으로 생성된 객체가 덮어져서 내부적인 constructor의 연결이 깨진것이다.

객체자체는 Jay이지만 Prototype Link 구조는 me -> Person() -> Person.prototype 의 연결형태가 된다.

Jay.prototype.constructor = Jay를 하여 깨진 constructor 링크를 복구할 수도 있다.

# 이해되는 reference

https://medium.com/@bluesh55/javascript-prototype-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-f8e67c286b67

http://www.nextree.co.kr/p7323/

https://medium.com/@han7096/javascript-core-3-%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85-prototype-c2da4c24820e

# 이해가 힘든 reference

http://insanehong.kr/post/javascript-prototype/