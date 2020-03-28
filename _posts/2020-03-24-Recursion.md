---
layout: post
title: Recursion
tags: [ComputerScience, Algorithms]
excerpt_separator: <!--more-->
---

# Recursion

Recursion은 자신의 알고리즘을 다시 불러와 사용하는 것이다.

Recursion을 통해 똑같은 자신의 function을 call stack에 저장하고 base case에 도달하면

push된 recursion function들을 하나씩 pop하면서 실행한다.

<!--more-->

# Big O notation

Fibonacci iterrative = O(n)

recursive = ##O(2^n)## (O(n^2)보다 훨씬 operation이 많다.)

## Although!! why they use recursive?

##Anything you can do with a recursion CAN be done iteratively(loop)##

pros  | cons
------------- | -------------
Readable  | Need Large Stack
DRY | 

javascript 6 version부터는 recursion을 call stack을 늘리지 않고 사용할 수 있다고 한다!!!!(a.k.a Tail call optimization)

https://2ality.com/2015/06/tail-call-optimization.html

이러한 기능은 다른 언어에서도 지원한다고 한다.

## Then, When we should use the recursion?

Everytime you are using a tree converting Something into a tree, consider Recursion.

1. Divided into a number of subproblems that are smaller instances of the same problem(문제를 작게 같은 문제들로 쪼갤 수 있다.)
2. Each instance of the subproblem is identical in nature(쪼갠 문제들의 operation이 모두 같다.)
3. The solution of each subproblem can be combined to solve the problem at hand(여러개로 쪼개진 문제들이 모여 문제를 해결 할 수 있다.)

정리하면...

- Divide and Conquer using Recursion
- BFS(Breadth First Search), DFS(Depth First Search)
- Tree Data structure or Tree Traversal
- Sorting

# Three Rules

복잡한것 같지만 이 법칙을 가지고 하나씩 생각하며 recursion을 해석하면 쉽다.

1. Identify the base case
2. Identify the recursive case
3. Get closer and closer and return when needed. Usually you have 2 returns.

# Explain!

## ex1

```javascript
var counter = 0;
function inception(){
 inception();
 console.log(counter);
}

inception();
```

이 logic의 결과는 Stack overflow(call stack overflow)이다. 끊임없이 자신을 불러내기만 하고 console.log에 도달하지 못해

아무것도 출력하지 못한다.

## ex2

```javascript
var counter = 0;
function inception(){
 console.log(counter)
 if(counter > 3) {
  return "done";
 }
 counter++;
 inception();
}

inception();
```

이때 inception을 호출했을 경우 결과는 Undefined라는 처리 결과가 나오게 된다.

Debug시에 보면 가장 첫번째는 return을 통해 'done'을 출력하지만 recursion으로 불린 function들은 return하지 않기 때문에 Undefined가 출력된다.

inception(inception('done')) = inception(Undefined) ...

## ex3

```javascript
var counter = 0;
function inception(){
 console.log(counter)
 if(counter > 3) {
  return "done";
 }
 counter++;
 return inception();
}

inception();
```

Recursion한 method를 return하라고 한다면 자신이 가진 return "done"을 return 하기에 가장 마지막에 pop되는 method에서 return을 실행하여 "done"이 출력된다.

inception(inception('done')) = inception('done') ...

# Excersise

## factorial

```javascript
function findFactorialIterative(number) {
  let answer = 1;
  for (let i = 2; i <= number; i++) {
    answer = answer * i;
  }
  return answer;
}

function findFactorialRecursive(number) {
  if(number === 2) {
    return 2;
  }
  return number * findFactorialRecursive(number - 1)
}

findFactorialIterative(5); //O(n)
findFactorialRecursive(5); //O(n)
```

## Fibonacci

My answer

```javascript
function fibonacciIterative(n){
  var result = 0;
  var foreNum = 0;
  var beforeNum = 1;
  if(n===1){
    return 1;
  }
  for(var i = 0; i < n-1; i++){
    result = foreNum + beforeNum;
    if(i%2 === 0){
      foreNum = result;
    }else{
      beforeNum = result;
    }
  }
  return result;
}
fibonacciIterative(3);

function fibonacciRecursive(n) {
  var beforeNum;
  if(n === 0 || n === 1){
    return n;
  }
  beforeNum = fibonacciRecursive(n-2);
  
  return beforeNum + fibonacciRecursive(n-1);
}

fibonacciRecursive(8)
```

Better Answer

```javascript
function fibonacciIterative(n){
  let arr = [0, 1];
  for (let i = 2; i < n + 1; i++){
    arr.push(arr[i - 2] + arr[i -1]);
  }
 return arr[n];
}
fibonacciIterative(3);

function fibonacciRecursive(n) {
  if (n < 2){
    return n;
  }
  return fibonacciRecursive(n - 1) + fibonacciRecursive (n - 2)
}

fibonacciRecursive(6)
```