---
layout: post
title: Make Inheritance in JS
tags: [JavaScript, Prototype, theories, OOP, Inheritance]
excerpt_separator: <!--more-->
---

# Make Inheritance

custom으로 만든 class혹은 function끼리의 Inheritance 관계를 만드는 방법과 Encapsulation을 하는 방법과 관련 이론들

<!--more-->

예시로 Person을 parent로 Student를 child로 둔다.

# Constructor functions

전통적으로 function으로 object를 만드는 방법

```javascript
const Person = function (firstName, birthYear){
    this.firstName = firstName;
    this.birthYear = birthYear;
};

Person.prototype.calcAge = function(){
    console.log(2037 - this.birthYear);
};

const Student = function(firstName, birthYear, course){
    //Student로 만들어진 new object = this
    //call에 의해 해당 object에 Person의 Property들이 함께한다.
    Person.call(this, firstName, birthYear);

    //이건 일반 function을 부를때 사용하는 방법이다. 따라서 Error(firstName이 정의되어 있지 않으니)
    //Person(firstName, birthYear);

    //parent에 추가된 property
    this.course = course;
}

//aka. linking prototypes
//이것으로 Student.prototype은 Person.prototype을 직접 바라보게 되었다.(자체 property들은 초기화)
//주의!! Person.prototype의 constructor는 Person이기 때문에 Student로 바꾸어 주어야 한다.
Student.prototype = Object.create(Person.prototype);

//Constuctor 수정
//Student의 prototype object의 constructor가 Person이 아닌 Student를 바라보게 올바르게 수정하였다!
Student.prototype.constructor = Student

/*
    Object.create순서
    - 만약 Object.create를 custom 후에 선언하면 Student.prototype은 빈 object로 overwrite되어버린다.
    - 따라서! Object.create를 한 후에 Student.prototype에 property들을 추가해야한다.
*/
Student.prototype.introduce = function(){
    console.log('My name is ${this.firstName} and I study ${this.course}');
}

const mike = new Student('mike', 2020, 'computer science');
mike.introduce();               //My name is mike and I study computer science
```

## 잘못된 예시

아래처럼 설정하면 Student도 Person의 Prototype object를 바라보기 때문에 new하면 Person의 Object가 만들어진다.

```javascript
Student.prototype = Person.prototype;
```

# ES6 Classes

