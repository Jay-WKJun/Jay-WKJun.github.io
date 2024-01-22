---
layout: post
title: Monotonic Stack
tags: [Algorithm, Stack, Stack, Array]
excerpt_separator: <!--more-->
---

# Monotonic Stack

Stack 자료구조를 활용하여 특정 원소보다 크거나 작은 근접한 원소를 O(n)으로 특정할 수 있게 해주는 방법입니다.

![monotonicStack]({{ "../assets/img/aboutComputerScience/monotonicStack.webp" | relative_url }})

<!--more-->

# 문제 예시

**"각 원소보다 뒤에 있는 원소 중에 보다 큰 원소가 뭔지 알고 싶다."**라는 문제가 있습니다.

예시로 한번 풀어보겠습니다.

## Stack 없이 풀어보기 - O(n^2)

직관적으로 O(n^2)의 방법을 떠올릴 수 있습니다.

```javascript
const arr = [2,1,2,4,3];
// 큰 요소가 없다면 -1로 남는다.
const res = new Array(arr.length).fill(-1);

// 이중 for문으로 최악의 시간복잡도는 O(n^2)이다.
for(let i = 0; i < arr.length; i++) {
	const iEl = arr[i];
	for(let j = i; j < arr.length; j++) {
		const jEl = arr[j];
		// i요소보다 큰 j요소가 나타나면 j위치를 기록한다.
		if (iEl < jEl) {
			res[i] = jEl;
		}
	}
}

return res;
```

## Stack으로 풀어보기 - O(n)

하지만, stack을 활용한다면, O(n)으로 해결할 수 있습니다.
문제가 오름차순을 요구하기 때문에, monotonicStack도 오름차순으로 유지합니다.

```javascript
const arr = [2,1,2,4,3];
// 오름차순 Stack order를 유지한다. 즉, 가장 최근에 들어온 Element가 가장 커야한다.
const monoStack = [];
const res = [];

for (let i = arr.length - 1; i >= 0; i--) {
	const el = arr[i];
	// 가장 최근에 들어온 stack el이 iEl보다 작으면 stack에서 모두 제거합니다.
	while (monoStack.length > 0 && monoStack[monoStack.length - 1] < el) {
		monoStack.pop();
	}
	// iEl보다 작은 stack 내용물들을 모두 제거했으면, stack의 가장 앞에 있는 element가 iEl 다음으로 큰 수입니다.
	res[i] = monoStack[0] ?? -1;
	// 자신을 넣어주고 다음으로 진행합니다.
	monoStack.push(el);
}

return res; // [4,2,4,-1,-1];
```

## 활용

stack에 element 대신, index를 넣으면 나보다 크거나 작은 index를 특정할 수도 있습니다.

## ref.

[https://www.youtube.com/watch?v=Dq_ObZwTY_Q](https://www.youtube.com/watch?v=Dq_ObZwTY_Q)

[Monotonic Stack, 단조스택이란?](https://iridescentboy.tistory.com/146)
