---
layout: post
title: Stacks and Queue
tags: [ComputerScience, DataStructure]
excerpt_separator: <!--more-->
---

# Stack and Queue

이 둘은 linear data structure로 모든 데이터의 접근과 동작을 순차적으로 해야 하는 특징이 있다.

stack은 선입후출(First In Last Out) Queue는 선입선출(First In First Out)이라는  특징이 있다.

<!--more-->

## 사용하는 이유

이 두 데이터 구조는 가장 적고 간단한 동작(push, pop)들로만 조작이 가능한데 이는 computer science에서는 중요한 역할을 한다.

이유는 동작이 적고 간단하기 때문에 범용성이 크기 때문이다.

## 사용 예

stack은 가장 최근에 저장한 정보를 알기에 가장 좋은 데이터 구조이다.

1. undo, redo = 뒤로가기, 앞으로 가기
2. 대부분의 프로그래밍 언어는 stack 방식을 기본으로 하고 있다.

queue는 실생활에서도 많이 쓰이는 데이터 구조 방식이다.

1. restaurant의 순서
2. 롤러코스터의 wating line
3. Uber!
4. printer queue
5. etc...

# Methods

여기서 헷갈리기 쉬운 점은 peek는 가장 먼저 나올 수 있는 데이터를 보여달라는 method이다.

따라서 stack에서는 가장 최신, queue에서는 가장 최후의 데이터를 보여준다.

## stack

methods  | Big O notation
------------- | -------------
Lookup  | O(n)
pop | O(1)
push | O(1)
peek | O(1)

## Queue

methods  | Big O notation
------------- | -------------
Lookup  | O(n)
enqueue | O(1)
dequeue | O(1)
peek | O(1)

Queue와 Array는 비슷하지만 Array는 index가 있어서 insert나 delete시에 모든 index에 영향을 주기 때문에

Queue에 비해 속도에서 크게 손해를 보게 된다.

# Pros & Cons

Pros  | Cons
------------- | -------------
Fast Lookups  | Slow Lookups
Fast peeks | 
Ordered | 
