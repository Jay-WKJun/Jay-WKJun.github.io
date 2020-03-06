---
layout: post
title: Linked Lists
tags: [ComputerScience, DataStructure]
excerpt_separator: <!--more-->
---

# Linked List

Linked List는 value와 node가 한 세트이며 node는 다음 순서의 value를 가리킨다.

<!--more-->

## Linked List vs Arrays

검색시 똑같은 O(n)의 시간 복잡도를 갖지만 Linked List는 random하게 메모리에 저장하고 Array는 데이터 바로 옆에 순서대로 저장하기 때문에

같은 시간복잡도를 갖더라도 Array가 검색에 더 빠르다.

# Methods

methods  | Big O notation
------------- | -------------
Prepend | O(1)
Append | O(1)
Lookup  | O(n)
insert | O(n)
delete | O(n)

Prepend와 Append는 head와 tail에 추가하는 것으로 list에 들어가지 않아도 바로 실행 가능하기 때문에 O(1)이다.

나머지 모든 행동은 순서를 따라가서 해당 value에 도착해 동작해야해서 O(n)이다.

# Pointer

Linked list에는 다른 데이터 구조에는 없는 개념인 Pointer가 있다. 하나의 node가 다른 node를 가르키는 것의 연속이기 때문이다.

pointer는 간단하게 특정 데이터가 있는 곳을 가리키는 reference를 말한다.

우리가 선언하는 객체의 이름도 일종의 pointer로서 해당 객체의 주소값을 담고 있는 것이다. 해당 객체의 주소값을 없애버리면 참조받지 않는 객체 데이터는 garbage collector에 의해 지워진다.

C언어에선 garbage collector의 기능을 사람이 해줘야한다. 이는 잘 다루면 컴퓨터의 효율면에서 성능을 크게 향상 시킬 수 있다.