---
layout: post
title: Asynchronous Advanced
tags: [JavaScript, AJAX, Theories, Asynchronous, vanillaCoding]
excerpt_separator: <!--more-->
---

# Asynchronous

async 관련해서 생각해볼 주제들, 추가설명들

<!--more-->

## promise의 장점

```javascript
//async인 get이 끝나고 바로 function이 실행된다.
$.get("www.google.com", function (resolve, reject) {
  //---logics-----
})

// [장점1] promise는 (1급)객체이므로 변수나 자료구조에 담을 수 있다.
const doneWork = fetch("www.google.com");

// [장점2] 원할 때 발동 할 수 있다.
if () {
  // [장점3] 콜백지옥에서 벗어날 수 있다.
  doneWork.then(
    //-------logics---------
  )
  .catch(
    //-------logics---------
  )
}else {
  // [장점4] 로직으로 쪼개 다른 콜백을 설정해줄 수도 있다.
  doneWork.then(
    //-------logics---------
  )
}
```

## async function

async function은 return 값이 Promise이다.

<b> await에 관해 </b>

await는 종료시점을 기다리는 것 뿐, 실행을 막지는 않는다.

```javascript
const a = new Promise(function (resolve, reject) {
  setTimeout(() => {
    resolve(666);
  }, 1000)
});

const b = new Promise(function (resolve, reject) {
  setTimeout(() => {
    resolve(777);
  }, 1000)
});

async function test (x) {
  return x + (await a) + (await b);
}

test(0);    //1443 1초 + a
/*
  1초가 걸린 이유
  내려오면서 a와 b가 실행되었고 await는 단순히 결과를 기다려주기만 하는 것이기 때문에 각자 1초가 걸려 끝났으니
*/

async function test2(x) {
  const a = () => {
    new Promise(function (resolve, reject) {
      setTimeout(() => {
        resolve(666);
      }, 1000)
    });
  }

  const b = () => {
    new Promise(function (resolve, reject) {
      setTimeout(() => {
        resolve(777);
      }, 1000)
    });
  }

  return x + (await a()) + (await b());
}

test2(0);  //1443 2초 + a
/*
  2초가 걸린 이유
  a()가 실행되고 결과값을 기다리는 동안 진행되지 않기 때문에 b()를 실행하지 않기 때문.
*/
```

## await를 쓰지 않는다면...?

```javascript
const a = new Promise(function (resolve, reject) {
  setTimeout(() => {
    resolve(666);
  }, 1000)
});

const b = new Promise(function (resolve, reject) {
  setTimeout(() => {
    resolve(777);
  }, 1000)
});

async function test (x) {
  var x, y;
  //####### await가 없다면 resolve값을 바로 주지 않고 Promise객체를 주기 때문에 따로 then을 해줘야 한다.
  a.then(
    x = result();
  )

  b.then(
    y = result();
  )

  return x + (a) + (b);
}

```