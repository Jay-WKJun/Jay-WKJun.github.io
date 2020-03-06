---
layout: post
title: Hash Tables
tags: [ComputerScience, DataStructure]
excerpt_separator: <!--more-->
---

# Hash Tables

특정 Key에 특정 value가 있는 배열구조형태

Hash Table이 중요한 점은 실생활에서도 매우 많이 사용되고 있고 따라서 *coding test에 반드시* 물어본다는 점에 있다.

그리고 많이 쓰이는 이유는! **보통 HashTable로 처리시간을 크게 단축할 수 있기 때문이다**(공간은 더 차지하지만)

<!--more-->

## Pros & Cons

Pros  | Cons
------------- | -------------
Fast Lookups  | UnOrdered
Fast inserts | Slow Key Iteration
Flexible keys | More Space Complexity

Key Iteration이 느린 이유는 컴퓨터에 의해 마구잡이로 저장된 Hash들을 메모리 단위에서 모두 찾아가야하기 때문이다.

Hash는 key값의 데이터를 저장해야하기 때문에 다른 데이터 구조보다 더 많은 공간을 소모한다.

## Hash Function

만약 내가 'Hello'라고 입력하면 Hash function을 통해 알 수 없는 암호같은 문자로 바뀌는데 이것이 Hash function이다.

중요한점은 입력이 같다면 출력이 같다는 점이고 이러한 특성을 *'Idempotent'*라고 한다.

이것을 사용하는 이유는 1. key값으로 데이터를 유추할 수 없게하여 보안 강화 2. key값을 통해 특정 데이터를 매우 빨리 찾을 수 있다. 는 점이 장점이다.

# Methods

methods  | Big O notation
------------- | -------------
insert | O(1)
Lookup  | O(1)
delete | O(1)
search | O(1)

array에서 처럼 index를 수정할 필요가 없어서 insert와 delete가 O(1)이다.

# Hash Collisions

같은 key에 1개 이상의 value가 들어왔을때, Hash Collision이라 한다.(일종의 Overflow)

Overflow된 value는 Linked list 형태로 같은 key에 종속되어 저장된다.[Seperate chaning](Overflow 해결방법은 굉장히 많다.)

중요한 점은 Overflow가 되면 reading과 writing의 속도가 O(n/k) = O(n)이 된다는 점이다.(k는 key의 갯수이다.)

# Map과 Set

Map은 무작위로 저장하는 것이 아닌 순서대로 저장한다.

Set은 value없이 key만 저장한다.