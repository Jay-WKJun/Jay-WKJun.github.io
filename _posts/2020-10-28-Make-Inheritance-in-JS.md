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
    this.firstName = firstName;
    this.birthYear = birthYear;
    //parent에 추가된 property
    this.course = course;
}

Student.prototype.introduce = function(){
    console.log('My name is ${this.firstName} and I study ${this.course}');
}

const mike = new Student('mike', 2020, 'computer science');
mike.introduce();
```