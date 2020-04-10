---
layout: post
title: Dynamic programing
tags: [ComputerScience, Algorithms]
excerpt_separator: <!--more-->
---

# Dynamic programing

Dynamic programing은 optimization(죄적화) technique 중 하나로 caching(cache를 이용)을 하면 모두 dynamic programing이라 할 수 있다.

하나의 problem을 작은 subproblem으로 쪼개고 subproblem을 하나씩 해결하고 그 solution을 cache에 저장해 나중에 똑같은 problem이 있을때 저장 된 것을 활용하는 방법이다.

<!--more-->

## implementing Dynamic programing 

Dynamic programing = Divide & Conquer + Memoization 으로 구성된다.

아래의 규칙을 따라 implement할 수 있는지 여부를 확인하고 implement할 수 있다.

1. Can be divided into subproblem?
2. Recursive Solution
3. Are there repetive subproblems?
4. Memoize subproblems

# When can we use dynamic programing?

Dynamic programing은 크게 1.Optimal SubStructure 2. Overlapping Subproblems 이 두가지의 경우에 사용 할 수 있다.

## Overlapping Subproblems

Definition : A problem is said to have overlapping subproblems if it can be broken down into subproblems which are reused several times

즉 subproblem으로 쪼개질 수 있고 subproblem을 여러번 사용(overlap)할 경우에 사용할 수 있다.(한번 풀었던 subproblem을 저장했다가 다시 사용하는 거니 당연하다.)

ex) fibonacci sequence - 전에 계산했던 결과를 반복하므로 그것을 저장했다가 다시 사용할 수 있다.

merge sort는 subproblem으로 쪼갤 수 있지만 subproblem이 겹치(overlap)지 않아서 dynamic program을 사용할 수 없다.

## Optimal Substructure(최적화 문제)

A problem is said to have optimal substructure if an opitmal solution can be constructed from optimal solutions of its subproblems.

최적화 문제(Optimization problems)란 여러개의 선택가능한 후보 중에서 최적의 해(Optimal value) 또는 최적의 해에 근접한 값을 찾는 문제를 일컫는다. 일반적으로 기계학습 분야에서는 비용함수(Cost function)를 최소화 또는 최대화 시키는 모델의 파라미터(parameter)를 구하게 되는데, 이것은 최적화 문제로 정의될 수 있다.

만약 알파벳으로 여러 지점이 나뉘어 져있는 지도에서 A -> B 로 가는 길이 가장 짧은 길이고 A -> C로 가는 길은 A - B - C 가 가장 짧으며 A -> D로 가는 길은 A - B - C - D일때, A -> D의 경우에 있는 경로에

A->B, A->C의 subproblem들이 가장 최적화 된 경로로 A->D의 경로에 사용되고 있으니 이것은 Optimal subproblem이라 할 수 있다.

# Memoization

Caching 방법 중 하나로 똑같은 입력을 똑같은 처리를 해야할 때, cache에 저장된 이미 처리된 결과를 그대로 가져다가 사용하는 것이다.

속도가 크게 개선되며 code도 효율적으로 짤 수 있기 때문에 중요하다.

## Caching

Caching은 어떤 문제나 해답, value 등을 cache에 저장해 나중에 활용하는 방법이다.

Backpack이라고 생각하면 쉽다. backpack에 필요한 것을 싸가서 학교에서 필요할 때 꺼내쓰는 것과 같다. 

```javaScript
function memoizeAddTo80(n) { 
  let cache = {};
  return function(n) {
    if (n in cache) {
      return cache[n];
    } else {
      console.log('long time');
      const answer = n + 80;
      cache[n] = answer;
      return answer;
    }
  }
}

const memoized = memoizeAddTo80();
console.log(1, memoized(6))
console.log(2, memoized(6))
console.log(2, memoized(5))

```

위의 결과로 처음엔 long time...이 출력되지만 두번째엔 출력되지 않고 이미 저장되어 있는 것을 가져올 것이며 마지막엔 새로 계산해야하므로 long time...이 출력 될 것이다.

field value인 cache를 지키기 위해 javascript의 closure라는 것을 사용했다. 따라서 해당 function을 호출시 cache가 초기화 되지 않아 Memoization을 제대로 구현 할 수 있다. (return function(n)로 처리부분만 묶음)


# Example (optimize fibonacci)

fibonacci는 O(2^n)의 complexity를 갖는데, Memoization을 이용한다면 O(n)으로 줄일 수 있다.

```javascript
//0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144, 233...
let calculations = 0;
function fibonacci(n) { //O(2^n)
  calculations++;
  if (n < 2) {
    return n
  }
  return fibonacci(n-1) + fibonacci(n-2);
}

function fibonacciMaster() { //O(n)
  let cache = {};
  return function fib(n) {
    calculations++;
    if (n in cache) {
      return cache[n];
    } else {
      if (n < 2) {
        return n;
      } else {
        cache[n] = fib(n-1) + fib(n-2);
        return cache[n];
      }
    }
  }
}
console.log('Slow', fibonacci(35))
console.log('we did ' + calculations + ' calculations');  //29860703
console.log('DP', fasterFib(100));
console.log('we did ' + calculations + ' calculations');  //69
```

algorithm은 그대로에 cache 저장과 출력만 추가 했을 뿐인데 complexity가 비약적으로 좋아졌다.

calculations의 결과를 보면 알 수 있다.

## Bottom up approach

이것 또한 dynamic programing의 방법 중 하나로 위의 memoization이 top down approach(recursion)이었다면 이 방법은 반대이다.

fibonacci에서 n부터 시작하는게 아니라 1,2부터 시작해서 n까지 연산해서 올라간다.(가장 작은 subproblem부터 타고 올라간다),전 계산 결과를 table에 담고 그것을 다시 사용한다. 이렇게 table에 결과들을 담아가는 것을 "Tabulant"라고 한다.

장점은 memoization을 사용하는 top-down에선 call stack을 쌓아놓고 base case에서 풀면서 오지만 bottom-up에선 전에 계산한 것을 바로 사용하기 때문에 stack을  사용하지 않아 space complexity가 훨씬 좋다.(fib(100000)인 경우 bottom-up은 overflow가 일어나지 않는다.)

code가 훨씬 직관적이고 recursion을 사용하지않지만 다른 case에 적용하기 어렵다는 단점이 있다.(code를 보면 반드시 저번에 계산했던 결과를 사용해야만 한다.)

```javascipt
function fibonacciMaster2(n) {
  let answer = [0,1];
  for ( let i = 2; i <= n; i++) {
    answer.push(answer[i-2]+ answer[i-1]);
  }
  return answer.pop();
}
```