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

모든 Object들이 가지고 있는 Prototype을 통해 inheritance가 가능하다.

Prototype property에 inherit하고 싶은 method와 property를 넣어두면 inherit이 가능하다.

다른 언어와는 다른 Javascript만의 상속방법

![PrototypeChain]({{ "/assets/img/aboutJavaScript/PrototypeChain.png" | relative_url }})

# Prototype Chain

모든 만들어진 Object들은 Object의 자식들로 항상 Object의 prototype을 가진다.

# Object.create vs Function constructor

## Object.create

Object.create(a, property object)를 하면 a를 새로 만들고 a에 있는 method들을 바로 inherit해서 prototype으로 가지고 property object를 통해 property를 설정해서 한번에 간단하게 object를 만들 수 있다.

이것의 장점은 매우 복잡한 inheritance structure를 아주 간단하게 다룰 수 있다는 것이다.

```javascript

var personProto = {
    //특징 : calculateAge는 prototype에 들어간다.
    calculateAge: function() {
        console.log(2016 - this.yearOfBirth);
    }
};

var john = Object.create(personProto);
john.name = 'John';
john.yearOfBirth = 1990;
john.job = 'teacher';

var jane = Object.create(personProto, {
    name: { value: 'Jane' },
    yearOfBirth: { value: 1969 },
    job: { value: 'designer' }
});

```

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

//"Callback Function"
//fn을 "CallBack Function"이라고 한다.
//fn에 넣어준 function이 발동되고 그 return값이 parameter에 들어가는게 아닌 arrayCalc function에 의해 fn이 발동되도록해주기 때문.

//fn은 fucntion을 받아들이는 argument로서 받아들이는 function에 따라 그 기능이 변한다.
function arrayCalc(arr, fn) {
    var arrRes = [];
    for (var i = 0; i < arr.length; i++) {
        //argument를 하나만 가진 fucntion만이 이곳에 들어 올 수 있다.
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
//(function에 ()를 해주지 않았기 때문에 function이 발동되지 않고 그대로 참조값만 들어간다.)
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

## Immediately Invoked Functions Expressions (IIFE)

재사용할 필요없는 function을 바로 사용하기 위한 방법

실행 원리 : 단순히 function만 쓰면 javascript parser가 function declaration이라고 판단하고 아무 일도 안 일어나지만 ()를 씌우고 parameter()를 붙이면 expression으로 판단하여 바로 실행한다.

특징

- 모듈화 : function안에 새로운 scope가 생기고 그 안에 variable들이 존재하여 function밖 scope의 영향을 받지 않을 수 있다.(독립적)
- 보안성 : function이 즉시 실행되고 종료되기 때문에 외부에 영향을 받지 않아 function안에 data를 안전하게 숨길 수 있다.

```javascript

//보통 방식
//하지만 function안의 data만 사용하고 function안의 data를 노출하고 싶지 않으므로 IIFE를 사용한다.
function game() {
    var score = Math.random() * 10;
    console.log(score >= 5);
}
game();

//IIFE방식, 즉시 발동되고 종료된다.
(function () {
    var score = Math.random() * 10;
    console.log(score >= 5);
})();

//score의 scope는 IIFE function안까지 이므로 이것은 Error이다(function안의 data를 숨길 수 있다.)
//console.log(score);

//이런식으로 IIFE에 parameter를 넣어줄 수 있다.
(function (goodLuck) {
    var score = Math.random() * 10;
    console.log(score >= 5 - goodLuck);
})(5);

```

## Closures

An inner function has alsways access to the variables and parameters of its outer function, even after the outer function has returned.

outer function이 return되어도 inner function은 outer function이 가지고 있던 variables과 parameter들을 그대로 사용할 수 있다.(inner function이 outer function안에 정의되어 있기 때문이다.)

```javascript

function retirement(retirementAge) {
    var a = ' years left until retirement.';
    //return하는 순간 retirement function의 execution context는 execution stack에서 pop되고 없어진다.
    //execution stack에서 사라지는 순간 scope chain에서 사라져야하지만 return되는 inner function의 scope chain에 그대로 남아있다.
    return function(yearOfBirth) {
        var age = 2016 - yearOfBirth;
        console.log((retirementAge - age) + a);
    }
}

//이 명령이 끝나면 retirement의 execution context는 사라지지만 scope chain은 inner function에 여전히 살아있다.
var retirementUS = retirement(66);
var retirementGermany = retirement(65);
var retirementIceland = retirement(67);

//inner function을 실행시키면 outer function의 scope chain을 이용하여 function을 실행한다.
retirementGermany(1990);
retirementUS(1990);
retirementIceland(1990);

//retirement(66)(1990);

```

추가 설명 reference

[https://poiemaweb.com/js-closure]


# 정리

- Every Javascript object has a ##prototype prorperty##, which makes inheritance possible in javascript
- The prototype property of an object is where we put methods and properties that we want other ##objects to inherit##
- The Constructor's prototype property is NOT the prototype of the Constructor itself, it's the prototype of ALL instances that are created throgh it
- When a certain method (or property)is called, the search starts in the object itself, and if it cannot be found, the search moves on to the object's prototype. This continues until the method is found : ##"Prototype Chain"##.