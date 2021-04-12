---
layout: post
title: Static and Instance
tags: [Class, Static, ComputerScience]
excerpt_separator: <!--more-->
---

# Static And Instance

Static method - Instacnce화 되지 않아도 사용할 수 있는 method

Instance method - 반드시 Object를 Instance화 하여 사용해야 하는 method

<!--more-->

# Static

static 키워드를 사용한 변수는 클래스가 메모리에 올라갈 때 자동으로 생성이 된다. 즉, 인스턴스(객체) 생성 없이 바로 사용가능 하다. 

그러므로, 객체를 생성하지 않아도 되니까 편리하고 속도도 빠르다.

## why static

자주 변하지 않는 일정한 값이나 설정 정보같은 공용자원에 대한 접근에 있어서 매번 메모리에 로딩하거나 값을 읽어들이는 것보다 일종의 '전역변수'와 같은 개념을 통해서 접근하는 것이 비용도 줄이고 효율을 높일 수있다. 

인스턴스 생성 없이 바로 사용가능 하기 때문에 프로그램 내에서 공통으로 사용되는 데이터들을 관리 할 때 이용한다.

# ref

[https://ifcontinue.tistory.com/2]