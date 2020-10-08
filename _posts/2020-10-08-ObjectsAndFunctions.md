---
layout: post
title: Objects And Functions
tags: [JavaScript, Function, Object]
excerpt_separator: <!--more-->
---

# Objects and Functions In Javascript

"EveryThing is an object (almost everything)"

자바스크립트의 가장 큰 특징이다

<!--more-->

# Two Big type of values

## primitive

- Numbers
- Strings
- Booleans
- Undefined
- Null

특징 : 변수 그 자체에 value를 가지고 있다.

## Object

Everything Else is Object!

특징 : 변수에는 Object의 참조값을 가지고 있고 실제 Object의 value는 참조값을 통해 찾아간 메모리 안에 있다.

따라서 (var a=b) 일 경우 b의 참조값이 a에 넘어간 것일 뿐 실제로 Object의 value들을 모두 복사한게 아니다.

# Prototype (or Constructor)

다른 Programing language에서 "Class"라고 불리는 것으로 Object의 blueprint라고 할 수 있다.

# Prototype-Based Inheritance

다른 언어와는 다른 Javascript만의 상속방법

![PrototypeChain]({{ "/assets/img/aboutJavaScript/PrototypeChain.png" | relative_url }})

# Prototype Chain

모든 만들어진 Object들은 Object의 자식들로 항상 Object의 prototype을 가진다.

# Object.create vs Function constructor

## Object.create

Object.create(a, property object)를 하면 a를 새로 만들고 a에 있는 method들을 바로 inherit해서 prototype으로 가지고 property object를 통해 property를 설정해서 한번에 간단하게 object를 만들 수 있다.

이것의 장점은 매우 복잡한 inheritance structure를 아주 간단하게 다룰 수 있다는 것이다.

## function constructor

반면 이 친구는 해당 object의 protype property를 prototype으로서 가지게 된다. (prototype property를 따로 지정해 줘야 한다.)

# Function

Javascript의 function은 아래와 같은 특성으로 인해 'First-Class Functions'이라고 불린다.

- A function is an instance of the Object type
- A function behaves like any other object
- We can store function in a variable
- we can pass a function as an argument to another function
- we can return a function from a function

## passing Functions as arguments

argument에 따라 function이 달라질 수 있다.

```javascript

var years = [1990, 1965, 1937, 2005, 1998];

//fn은 fucntion을 받아들이는 argument로서 받아들이는 function에 따라 그 기능이 변한다.
function arrayCalc(arr, fn) {
    var arrRes = [];
    for (var i = 0; i < arr.length; i++) {
        arrRes.push(fn(arr[i]));
    }
    return arrRes;
}

function calculateAge(el) {
    return 2016 - el;
}

function isFullAge(el) {
    return el >= 18;
}

function maxHeartRate(el) {
    if (el >= 18 && el <= 81) {
        return Math.round(206.9 - (0.67 * el));
    } else {
        return -1;
    }
}

//argument인 fn에 어떤 function을 넣어주느냐에 따라 fn의 function이 바뀌고 있다.
var ages = arrayCalc(years, calculateAge);  //[26, 51, 79, 11, 18]
var fullAges = arrayCalc(ages, isFullAge);  //[true, true, true, false, true]
var rates = arrayCalc(ages, maxHeartRate);  //[189, 173, 154, -1, 195]

console.log(ages);
console.log(rates);

```

## Functions returning functions

Function을 return하면 그것을 function의 기능 그대로 사용할 수 있다.

```javascript

function interviewQuestion(job) {
    if (job === 'designer') {
        //function을 return
        return function(name) {
            console.log(name + ', can you please explain what UX design is?');
        }
    } else if (job === 'teacher') {
        return function(name) {
            console.log('What subject do you teach, ' + name + '?');
        }
    } else {
        return function(name) {
            console.log('Hello ' + name + ', what do you do?');
        }
    }
}

//teacherQuestion에는 teacher에 해당하는 function이 들어있다.
var teacherQuestion = interviewQuestion('teacher');
//designerQuestion에는 designer에 해당하는 function이 들어있다.
var designerQuestion = interviewQuestion('designer');

//var에 받은 function은 이렇게 재활용이 가능하다.
teacherQuestion('John');    //What subject do you teach, John?
designerQuestion('John');   //John, can you please explain what UX design is?
designerQuestion('jane');   //jane, can you please explain what UX design is?
designerQuestion('Mark');   //Mark, can you please explain what UX design is?
designerQuestion('Mike');   //Mike, can you please explain what UX design is?

//var를 이용하지 않고 한번에 가능하다.
interviewQuestion('teacher')('Mark');   //What subject do you teach, Mark?

```


# 정리

- Every Javascript object has a ##prototype prorperty##, which makes inheritance possible in javascript
- The prototype property of an object is where we put methods and properties that we want other ##objects to inherit##
- The Constructor's prototype property is NOT the prototype of the Constructor itself, it's the prototype of ALL instances that are created throgh it
- When a certain method (or property)is called, the search starts in the object itself, and if it cannot be found, the search moves on to the object's prototype. This continues until the method is found : ##Prototype Chain##.