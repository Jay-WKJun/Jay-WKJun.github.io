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

ES6에 추가된 class syntax로 object를 만드는 방법

주목할 점은 class의 간단한 extends방법으로

- instance의 prototype을 부모 prototype object로 알아서 설정
- 어긋날 수 있는 constructor를 올바르게 만들어진 class의 constructor를 바라보게 함

을 알아서 해준다는 점이다. (전통적인 방법에선 수동으로 수정해주어야 했다.)

```javascript
class PersonCl{
    constructor(firstName, birthYear){
        this.firstName = firstName;
        this.birthYear = birthYear;
    }

    calcAge(){
        console.log(2037 - this.birthYear);
    }
}

class StudentCl extends PersonCl{
    constructor(firstName, birthYear, course){
        //super는 반드시 가장 먼저 나와야한다.
        //이유 : super를 통해 subclass와 연동하여 this를 올바르게 설정해주기 때문이다.
        super(firstName, birthYear);
        this.course = course;
    }

    introduce(){
        console.log('My name is ${this.firstName} and I study ${this.course}');
    }

    //Overrride
    calcAge(){
        console.log('I born in ${this.birthYear}');
    }
}

const martha = new StudentCl('martha', '2012', 'Computer Science');
martha.introduce();         //'My name is martha and I study Computer Science
martha.calcAge();           //I born in 2012
```

# Object.create

new 와의 특징은 constructor의 걱정을 할 필요가 없다는 것.

object와 object를 그대로 잇는 것이기 때문

```javascript
const PersonProto = {
    calcAge(){
        console.log(2037 - this.birthYear);
    },

    init(firstName, birthYear){
        this.firstName = firstName;
        this.birthYear = birthYear;
    },
};

const steven = Object.create(PersonProto);

//자신의 property가 하나도 없는 object와 그 prototype을 PersonProto로 가진 object생성
const StudentProto = Object.create(PersonProto);
StudentProto.init = function(firstName, birthYear, course){
    PersonProto.init.call(this, firstName, birthYear);
    this.course = course;
}
StudentProto.introduce = function(){
    console.log('My name is ${this.firstName} and I study ${this.course}');
}

//자신의 property가 하나도 없는 object와 그 prototype을 StudentProto로 가진 object생성
const jay = Object.create(StudentProto);
jay.init('Jay', 2010, 'Computer Science');
jay.introduce();                                //My name is Jay and I study Computer Science
```