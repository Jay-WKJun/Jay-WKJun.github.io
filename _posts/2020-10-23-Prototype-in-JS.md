---
layout: post
title: Prototype in JS
tags: [JavaScript, Prototype, theories]
excerpt_separator: <!--more-->
---

# prototype

객체가 만들어지기 위해 그 객체으 모태가 되는 것

java의 class과 같다고 보면 된다.

__proto__가 바로 객체 원형에 대한 숨겨진 연결

객체가 만들어지기 위해서는 자신을 만드는 데 사용된 원형인 프로토타입 객체를 이용하여 객체를 만듭니다.

함수를 정의하면 함수만 생성되는 것이 아닌 Prototype Object도 같이 생성된다.

객체는 언제나 함수로 생성된다.

Constructor(생성자)는 해당 함수의 prototype Object에 의해 권한이 부여됨 = 함수만이 constructor를 사용할 수 있다.



## 구성

함수에는 프로토타입 객체(Prototype Object)를 참조하는 'prototype 속성'과 객체 멤버인 'proto 속성이 참조하는 숨은 링크'가 있습니다.

# 함수와 내부구조

JavaScript에서는 함수를 정의하고, 파싱단계에 들어가면, 내부적으로 해당 함수 이름과 같은 Prototype Object도 같이 생성이 됩니다.

함수 멤버로 숨겨진 prototype 속성이 있습니다. 이 prototype 속성은 Prototype Object를 가리킵니다.

![HowFunctionMadeOf]({{ "/assets/img/aboutJavaScript/WhenDeclareAFunction.png" | relative_url }})

Prototype Object는 일반적인 객체와 같으며 기본적인 속성으로 constructor와 __proto__를 가지고 있습니다.

![PrototypeObject]({{ "/assets/img/aboutJavaScript/FunctionsprototypeObject.png" | relative_url }})

constructor는 Prototype Object와 같이 생성되었던 함수를 가리키고 있습니다. 해당 함수를 가리키며 객체 안에 복사해놓고 있습니다. 이것을 이용해 새로운 instance를 만들어냅니다.

__proto__는 Prototype Link입니다.

Prototype Object는 일반적인 객체이므로 속성을 마음대로 추가/삭제 할 수 있습니다. kim과 park은 Person 함수를 통해 생성되었으니 Person.prototype을 참조할 수 있게 됩니다.

# 객체의 내부구조

모든 객체는 반드시 '\_\_proto\_\_'라고 하는 속성(property)을 가지며 속성안엔 해당 객체를 만들때 사용한 원형인 Prototype Object가 있다.

'\_\_proto\_\_'는 Prototype Link로 이 속성은 객체가 만들어지기 위해 사용된 원형인 프로토타입 객체를 숨은 링크로 참조하는 역할을 합니다.

즉, __proto__는 객체가 생성될 때 조상이었던 함수의 Prototype Object를 가리킵니다.

prototype 속성은 함수만 가지고 있던 것과는 달리 __proto__속성은 모든 객체가 빠짐없이 가지고 있는 속성입니다.

# prototype inheritance (프로토타입 상속)



# Prototype Object

같은 원형으로 생성된 객체가 공통으로 참조하는 공간입니다. 프로토타입 객체의 멤버를 읽는 경우에는 객체 또는 함수의 prototype 속성을 통해 접근할 수 있습니다. 하지만 추가, 수정, 삭제는 함수의 prototype 속성을 통해 접근해야 합니다.


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

# 이해되는 reference

https://medium.com/@bluesh55/javascript-prototype-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-f8e67c286b67

http://www.nextree.co.kr/p7323/

# 이해가 힘든 reference

http://insanehong.kr/post/javascript-prototype/