---
layout: post
title: Searching
tags: [ComputerScience, Algorithms]
excerpt_separator: <!--more-->
---

# Searching

검색은 실생활에서도 자주 사용하는 것으로 Sorting보다는 훨씬 자주 접한다.

<!--more-->

# Linear search

처음부터 하나씩 확인해가며 찾는 search 방법. 따라서 O(n)이다.

# Binary search

중간값을 골라서 찾는 값과 비교, 찾는 값이 중간값보다 오른쪽이면 왼쪽 모두 버리고 반대도 마찬가지이다. 혹은 값이 크거나 작거나를 비교해서 진행할 수도 있다. 따라서 O(log (n))

이것은 Binary search Tree와 동일한 원리로 검색이 이루어진다. 따라서 sorting되어 있을 때 가장 빠르다(O(log (n)))는 점도 똑같다.