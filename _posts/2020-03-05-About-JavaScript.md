---
layout: post
title: About JavaScript
tags: [Tech, Javascript]
excerpt_separator: <!--more-->
---

# JavaScript

javascript란 single thread 이고 non-blocked 언어이다.

<!--more-->

# Program이란?

- Allocate Memory
- parse and execute(=read and run command)

# web browser & javascript engine

각 web browser는 각자의 javascript engine이 있고 chrome은 V8 engine을 사용한다.

Engine은 'Memory heap'과 'Call stack'으로 이루어져있다.

'Memory heap'은 데이터가 저장되는 곳=, 'Call stack'은 code가 read, execute되는 곳이다.

## Memory leak

사용하지 않는, 그래서 쓸모없게 된 variable이 있을 때 Memory leak라고 한다.

특히 전역변수는 생성하면 프로그램이 끝날때까지 Memory heap을 차지하기 때문에 memory leak의 원인이 되어 좋지 않다.

## Call stack

명령을 stack에 push하고 실행하고 pop하는 형식으로 명령을 계속 실행해 나간다.

# Single Threaded

Single Thread = one Call stack 이라는 말로 한번에 하나의 명령을 수행한다.

## Deadlock & multi thread

multi thread의 단점으로 Deadlock을 들 수 있다. 복잡하게 여려 명령을 쪼개서 실행하기 때문이다.

## Stack Overflow

말 그대로 Stack의 용량보다 더 많은 양의 Data혹은 command가 stack에 들어올 때 생기는 오류이다.

Stack Overflow는 이런식으로 일으킬 수 있다.

```javascript
function foo(){
	foo()
}

foo()
```

Recursion을 통해 foo()가 무한히 stack에 쌓여 overflow가 일어난다.

# non-blocking & asynchronous

javascript는 single thread이 하고 있는 작업을 막지 않는(non-blocking) asynchronous한 언어이다.

## synchronous vs asynchronous

synchronous는 선생님에게 전화하는것과 같다. 답변을 받기 위해 계속 전화하고 그동안 아무것도 하지 못하는 상태이다.

즉, 맡은 작업이 끝날 때 까지 다른 작업이 방해하지 못하게(blocked)하는 것이다.

asynchronous는 선생님에게 문자를 보내는 것과 같다. 질문을 보내놓고 다른걸 하다가 답변을 받으면 그것을 보고 해결하면 된다.

즉, 맡은 작업이 끝나지 않아도 잠시 미뤄두고 다른 작업을 할 수 있다는 것이다.(non-blocked)(callback)

setTimeout이 아주 그 예이다. web API중 하나로 특정 시간동안 특정 method를 미뤄뒀다가 callback하여 다시 작업을 처리 할 수 있다.

ex) EventLoop, onClick, onLoad etc...

## EventLoop

EventLoop는 call stack이 비어있을 때, 남은 작업을 callback 해주는 loop로 callback queue를 하나씩 차례대로 callback해준다.

# My Stack

```java
class Node {
	String value;
	Node next;

	public Node(String a) {
		this.value = a;
		this.next = null;
	}

	@Override
	public String toString() {
		return "Node [value=" + value + ", next=" + next + "]";
	}
	
}

public class Stack {

	String top;
	String bottom;
	int length;
	
	public Stack() {
		this.top = null;
		this.bottom = null;
		this.length = 0;
	}
	
	public static void main(String[] args) {
		Stack stack = new Stack();
		Node head = new Node(null);
		String youtube = "youtube";
		String google = "google";
		String udemy = "udemy";
		head = stack.push(youtube, head);
		stack.peek(head);
		head = stack.push(google, head);
		stack.peek(head);
		head = stack.push(udemy, head);
		stack.peek(head);
		stack.pop(head);
		stack.peek(head);
		stack.pop(head);
		stack.peek(head);
		stack.pop(head);
		stack.peek(head);
		stack.pop(head);
	}
	
	void peek(Node node){
		//값이 없다면
		if(node.value == null) {
			System.out.println("nothing to peek man...");
			System.out.println();
			return;
		}
		
		Node currentNode = node;
		
		//최종 값까지 가서
		while(currentNode.next != null) {
			currentNode = currentNode.next;
		}
		
		//최종 값 출력
		System.out.println(currentNode.value);
		System.out.println();
	}
	
	Node push(String value, Node node) {
		//최초라면
		if(node.value == null) {
			return new Node(value);
		}
		
		Node currentNode = node;
		while(currentNode.next != null) {
			currentNode = currentNode.next;
		}
		
		currentNode.next = new Node(value);
		length++;
		
		return node;
	}
	
	Node pop(Node node) {
		//값이 없다면
		if(node.value == null) {
			System.out.println("nothing to pop...");
		}
		
		//마지막 값만 남아 있다면
		if(node.next == null) {
			node.value = null;
			length--;
			return node;
		}
		
		Node currentNode = node;
		//최종 바로 전 값까지 가서
		for (int i = 1; i < length; i++) {
			currentNode = currentNode.next;
		}
		
		//pointer를 제거한다
		currentNode.next = null;
		length--;
		
		return node;
	}
}
```

