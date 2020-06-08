---
layout: post
title: Spring Backend Study week1
tags: [FrameWork, Java, Spring, SpringBoot, onlineStudy, springBackEnd, programmers]
excerpt_separator: <!--more-->
---

# week1

드디어 스터디 시작이다.

# class note

## SRP(단일 책임 원칙)

하나의 class는 하나의 기능만 하자

## what is java bean?

Java Bean 은 데이터를 표현하는 것을 목적으로 하는 자바 클래스이다. 

특별한 것은 없고 Java Bean 규약 에 맞춰서 만든 클래스를 뜻한다.

Java Bean 규약

1. 기본생성자가 존재해야한다.
2. 모든 멤버변수의 접근제어자는 private이다.
3. 멤버변수마다 getter/setter가 존재해야한다. (속성이 boolean일 경우 is를 붙힘)
4. 외부에서 멤버변수에 접근하기 위해서는 메소드로만 접근할 수 있다.
5. Serializable(직렬화)가 가능해야한다.

## java bean configuration을 xml이 아닌 java로 할때의 장점

xml은 단순 설정파일이다.

하지만 java로 설정을 하게 되면 logic을 추가할 수 있어 dynamic하게 설정을 만들어 줄 수 있다.

따라서 xml로 configuraion을 쓰는 곳을 탈출각이다

# Tips

책 : 토비의 스프링 3.1 (스프링의 정석책같은 필독서다.)(컴퓨터 공학적인 이야기가 많이 들어있어 다른 프로젝트에서도 도움이 많이 될것이다.)

## domain entity

user 같은 domain entity에 login 같은 고유의 logic이 들어갈 수 있도록 해주는게 좋다.

## 면접

spring의 전체적인 흐름을 물어본다(앞단은 servlet으로부터 시작된다.)

dispatcherservlet 하나만 servlet이 아니라 새로 만들수도 있다.

application context는 하나가 아니라 hierachy하게 있다.

# spring security

spring security는 크게 2가지 있다.

Authentification(인증)

Authorization(인가)

------------------------------------------------------------

# 20.05.10
POST 등록까지 완료

## 구현하면서...

간만의 spring개발이고 boot는 처음이었으니 효율적인 코드와 구조 보다는

과거의 배웠던 것을 되살리고 어떻게든 돌아가게만 할 수 있도록 만들기 위해 노력함.

## 궁금한 점

1. POST에서 어떻게 JSON의 data를 보낼 수 있는지...?
2. 아직은 옛날방식인 Autowired를 사용하는데 seminar에서 봤던 constructor를 이용한 autowire하는 방법을 아직 잘 모르겠다.
3. validation을 위한 @Entity와 @service에서 쓸 수 있는 여러가지 annotation이 있다고하는데 아직 잘 모르겠다.
4. Mock서비스가 뭔가...?
5. HTTP요청을 처리하는 컨트롤러에 사용하는 api들의 이름은?

## 해야할 것

- 하드코딩된 것들 정리
- validation처리
- mock service 공부
- HTTP요청 처리 관련 공부와 구현