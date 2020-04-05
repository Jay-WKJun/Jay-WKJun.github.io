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

# Traversal

Graph와 Tree의 데이터구조에서 모든 원소들(n)을 들를 때 Traversal이라고 한다. 모든 원소를 거치기 때문에 O(n)이다.

visualGo의 Traversal Animation

https://visualgo.net/en/dfsbfs?slide=1

## Breath First Search

이것은 가로로 훑는 방법으로 Tree일땐 왼쪽에서 오른쪽으로~ 밑으로 내려가서 끝까지 반복이다.

DFS보다 많은 메모리가 필요하다. 이유는 한 level의 node를 모두 저장해야 하기 때문이다.

pros  | cons
------------- | -------------
Shotest path  | More Memory
Closer Nodes | 

pros의 말은 root node에서 가장 가깝다는 말이다.

shortest path는 모든 level의 node를 확인하고 가기 때문에 어떤 node까지 가는데 가장 최적화 된 path를 찾을 수 있다는 것이다.

```javascript
  BreadthFirstSearch(){
    let currentNode = this.root;
    let list = [];
    let queue = [];
    queue.push(currentNode);

    while(queue.length > 0){
      currentNode = queue.shift();
      list.push(currentNode.value);
      if(currentNode.left) {
        queue.push(currentNode.left);
      }
      if(currentNode.right) {
        queue.push(currentNode.right);
      }
    }
    return list;
  }
  
  BreadthFirstSearchR(queue, list) {
    if (!queue.length) {
      return list;
    }
    const currentNode = queue.shift();
    list.push(currentNode.value);
    
    if (currentNode.left) {
      queue.push(currentNode.left);
    }
    if (currentNode.right) {
      queue.push(currentNode.right);
    }
    
    return this.BreadthFirstSearchR(queue, list);
  }
```

## Depth First Search

세로로 훑는 방법으로 끝까지 내려갔다가 올라와서 훑고 반복이다. 이것도 주로 왼쪽부터 시작한다.

BFS보다 적은 메모리가 든다. 이유는 각 level의 모든 child들을 가지고 있을 필요가 없기 때문이다.(필요할때 child를 찾아가면 됨.) Space complexity는 깊이 끝까지 찾기 때문에 O(depth)이다.(code를 보면 알 수 있다.)

DFS를 시작할 땐, inorder(왼 중 오), preorder(중 왼 오), postorder(왼 오 중)의 방법이 있다.

preorder는 tree의 모양대로 정렬이 되어있기 때문에 tree를 재정렬할 떄 매우 유용하다.

```javascript
//     9
//  4     20
//1  6  15  170

InOrder - [1, 4, 6, 9, 15 ,20 ,170]
PreOrder - [9, 4, 1, 6, 20, 15, 170]
PostOrder - [1, 6, 4, 15, 170, 20, 9]
```

pros  | cons
------------- | -------------
Less Memory  | can get slow
Does path exist? | 

Target이 존재하는지, target까지의 path가 존재하는지 깊게 들어가기 때문에 빠르게 찾을 수 있다.

Tree와 Graph는 매우 깊기때문에 전체 검색 속도가 느릴 수 있다.

```javascript
  DFTPreOrder(currentNode, list) {
    return traversePreOrder(this.root, []);
  }
  DFTPostOrder(){
    return traversePostOrder(this.root, []); 
  }
  DFTInOrder(){
    return traverseInOrder(this.root, []);
  }


function traversePreOrder(node, list){
  list.push(node.value);
  if(node.left) {
    traversePreOrder(node.left, list);
  }
  if(node.right) {
    traversePreOrder(node.right, list);
  }
  return list;
}

function traverseInOrder(node, list){
  if(node.left) {
    traverseInOrder(node.left, list);
  }
  list.push(node.value);
  if(node.right) {
    traverseInOrder(node.right, list);
  }
  return list;
}

function traversePostOrder(node, list){
  if(node.left) {
    traversePostOrder(node.left, list);
  }
  if(node.right) {
    traversePostOrder(node.right, list);
  }
  list.push(node.value);
  return list;
}
```

# Validate Binary Search Tree

interview에서 자주 묻는 질문이다. binary search tree가 규칙대로 되어있는가를 확인하는 것이다. 보통 BFS가 유용하다.

https://leetcode.com/problems/validate-binary-search-tree/

# Graph Traversals

## BFS, DFS

Tree는 Graph의 한 종류이므로 같은 원리로 Traversal을 할 수 있다.

중요한건 BFS와 DFS의 쓰임인데 Graph는 실생활과 밀접한 관련이 있는 데이터 구조이기 때문에, 실제로도 많이 사용되고 있다.

## BFS in Graph

BFS - Shortest path

ex) Amazon의 recommendation system, Facebook의 현재 가장 가까운 친구, Google map의 길찾기

## DFS in Graph

DFS - Check to see if it exists, maze를 풀어내는 것과 같다.

# Dijkstra & Bellman - Ford Algorithms

둘 다 weighted graph에서 shortest path를 찾는 알고리즘으로 효율적이며 각자 특징이 있다.

Dijkstra - negative edge가 있으면 사용 불가능, Bellman - Ford보다 더 효율적이다.

Bellman - Ford - negative weight가 있어도 사용 가능, Dijkstra비해 complexity가 비효율적(worst case O(n^2))