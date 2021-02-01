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

call method에 대한 자세한 설명

[https://webclub.tistory.com/394]

[https://velog.io/@rohkorea86/this-%EC%99%80-callapplybind-%ED%95%A8%EC%88%98-mfjpvb9yap]

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

# Encapsulation

Javascript는 Java나 c언어처럼 언어 레벨에서 private를 지원하진 않는다.

하지만 convention을 통해 가짜로 private를 만들어 낼 수 있다.

## Protected

앞에 '\_'를 붙여서 function안에서만 사용하는 property 혹은 method라는 것을 알린다.

## Private

현재 private 기능을 개발 진행중이며 완벽하지는 않지만 Google Chrome에서는 지원하고 있다.

개발 진행중이니 눈여겨 보아야한다.

```javascript
class Account{
    
    //변수명 앞에 #을 붙여주면 private이 된다.
    //private field는 반드시 전역변수로 따로 설정해주어야 클래스 내에서 사용할 수 있다.
    #movement = [];
    #pin;

    constructor(owner, currency, pin){
        this.owner = owner;
        this.currency = currency;
        this.#pin = pin;
    }

    //method도 마찬가지
    //하지만 method가 아닌 field취급을 한다 = 미완성
    #approveLoan(){
        return true;
    }
}

const account = new Account('jim', 2323000, 12313);
console.log(account.#movement);         //error
console.log(account.#approveLoan())     //error
```

# chaining methods

여러 method를 한번에 이어서 사용하는 방법

```javascript
class Account{
    
    #movement = [];
    #pin;

    constructor(owner, currency, pin){
        this.owner = owner;
        this.currency = currency;
        this.#pin = pin;
    }

    deposit(val){
        movement.push(val);
        //return없이는 undefined를 return하기 때문에 자신을 반환하면 다시 자신 안의 method를 사용할 수 있으므로 chining이 가능하다.
        return this;
    }
    withdrawal(val){
        movement.push(-val);
        //이런식으로 특별한 return이 없는 method에 자신을 반환해 method chaining이 가능하도록 할 수 있다.
        return this;
    }
}

const accout = new Account('hey', 20000000, 1221);
account.deposit(123).deposit(346).withdrawal(10);
```