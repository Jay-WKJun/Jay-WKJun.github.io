---
layout: post
title: Doubly Linked Lists
tags: [ComputerScience, DataStructure]
excerpt_separator: <!--more-->
---

# Doubly Linked List

Single Linked List와는 다르게 Pointer가 2개이다. 다음 Data를 가리키는 pointer, 그 전 Data를 가리키는 pointer가 그것이다.

pointer가 하나 더 있기 때문에 Memory가 2배로 소모되지만 더 flex하게 Data를 다룰 수 있다.(특히 앞에서만 시작했던 linked list와는 달리 뒤에서도 접근이 가능하다.)

# reference

https://visualgo.net/en/list?slide=1a

# My codes (Javascript)

```javascript
class Node{
    constructor(val){
        this.val = val;
        this.next = null;
        this.prev = null;
    }
}

class DoublyLinkedList {
    constructor(){
        this.head = null;
        this.tail = null;
        this.length = 0;
    }
    push(val){
        var newNode = new Node(val);
        if(this.length === 0){
            this.head = newNode;
            this.tail = newNode;
        } else {
            this.tail.next = newNode;
            newNode.prev = this.tail;
            this.tail = newNode;
        }
        this.length++;
        return this;
    } 
    pop(){
        if(!this.head) return undefined;
        var poppedNode = this.tail;
        if(this.length === 1){
            this.head = null;
            this.tail = null;
        } else {
            this.tail = poppedNode.prev;
            this.tail.next = null;
            poppedNode.prev = null;
        }
        this.length--;
        return poppedNode;
    }
    shift(){
        if(this.length === 0) return undefined;
        var oldHead = this.head;
        if(this.length === 1){
            this.head = null;
            this.tail = null;
        }else{
            this.head = oldHead.next;
            this.head.prev = null;
            oldHead.next = null;
        }
        this.length--;
        return oldHead;
    }
    unshift(val){
        var newNode = new Node(val);
        if(this.length === 0) {
            this.head = newNode;
            this.tail = newNode;
        } else {
            this.head.prev = newNode;
            newNode.next = this.head;
            this.head = newNode;
        }
        this.length++;
        return this;
    }
    get(index){
        if(index < 0 || index >= this.length) return null;
        var count, current;
        if(index <= this.length/2){
            count = 0;
            current = this.head;
            while(count !== index){
                current = current.next;
                count++;
            }
        } else {
            count = this.length - 1;
            current = this.tail;
            while(count !== index){
                current = current.prev;
                count--;
            }
        }
        return current;
    }
    set(index, val){
        var foundNode = this.get(index);
        if(foundNode != null){
            foundNode.val = val;
            return true;
        }
        return false;
    }
    insert(index, val){
      if(index < 0||index >= this.length){
        return;
      }
      if(index === 0){
        return this.unshift(val);
      }
      if(index === this.length){
        return this.push(val);
      }
      var newNode = new Node(val);
      var foundNode = this.get(index-1);
      var nextNode = foundNode.next;
      foundNode.next = newNode;
      nextNode.prev = newNode;
      newNode.next = nextNode;
      newNode.prev = foundNode;
      this.length++;
      return this;
    }
    remove(index){
      if(index === 0){
        return this.shift();
      }
      if(index === this.length-1){
        return this.pop();
      }
      var foundNode = this.get(index-1);
      var nextNode = foundNode.next;
      var prevNode = foundNode.prev;
      nextNode.prev = prevNode;
      prevNode.next = nextNode;
      foundNode = null;
      this.length--;
      return true;
    }
}
```