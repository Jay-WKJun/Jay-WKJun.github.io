---
layout: post
title: 오답노트
tags: [StudyNote, Incorrects]
excerpt_separator: <!--more-->
---

# 이 페이지의 목적

틀렸던 코딩 문제들의 모음집

<!--more-->

# Problem : getHeights, Binary search Trees

https://www.hackerrank.com/challenges/30-binary-search-trees/problem

# 스스로 생각했던 풀이 방법

-Node객체를 리턴하는 메소드로 search하고 마지막 가지를 지워가는 형식. 한쪽 줄기를 모두 null로 만들고 돌아오면 count를 max값과 확인하고 초기화하고 다시 다른 줄기로 가는 형식 그렇게 반복해서 root가 null이면 count를 하나 더하고 종료 -> 이 때 count를 search과정에서 받아야하는데 return값이 하나이므로 불가능. class변수로 두면 되지만 기존에 주어진 Node의 root객체로 다시 돌아오기가 힘듦. 

-아니면 자신을 recursion해서 Node를 스스로 갱신해가면서 똑같은 방식을 하는 방법 -> 기본적으로 주어진 method가 int라 불가, 가지 node를 지우고 다시 root로 돌아오기가 불가능.

-입력되는 숫자의 갯수를 이용해서 log를 계산한 후 가장 마지막에 있을 가지들의 갯수를 계산해 for문의 반복수를 제한하여 가지들을 한번씩 들르고 돌아오는 방법

# 정답

```java
public static int getHeight(Node root){
    if (root == null){
        return -1;
    }
    else{
        return 1 + Math.max( getHeight(root.left), getHeight(root.right) );
    }
}
```

양 옆의 가지가 비어있을 때 까지 recursion하고 빈 곳에서 -1을 받고 그 위로 0를 리턴한다. 이렇게 되면 양쪽 중 긴쪽의 return값이 더 크기 때문에 큰 값만 return되고 최종적으로 가장 큰 값만 return되어 가장 큰 길이를 얻을 수 있다.

# 복습 노트 (직접 구현하면서 복습했던 것들)

BFS, DFS 다시

# 반성 노트

퀵소트도 못했던 이유
1. 스스로 어느정도까지 범위를 줘야하는지도 헷갈리고 있었다.(for문 범위나 array범위를 정확히 몰라서 에러가 나고 이게 혼돈의 카오스)

      = 기본도 안되어 있었다.

2. 모든걸 한꺼번에 하려 했다.(이번엔 divide기능 먼저 확실하게 하고 기능을 하나씩 추가해가면서 만들었다.)

  2-1. 이번엔 기능을 한번에 하나씩 추가했다.

  2-2. 이번엔 해야하는 일(논리구조)을 쪼개서 하나씩 만족시켜 나갔다.(divide를 연속으로 실행 후 1개가 되었을 때 merge연속시행)