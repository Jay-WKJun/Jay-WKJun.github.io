---
layout: post
title: Mixin repository
tags: [FrameWork, Java, Spring, SpringBoot]
excerpt_separator: <!--more-->
---

# Spring Boot

Spring Boot는 Spring의 설정들을 간편하게 설정할 수 있도록 나온 spring framework의 파생형 framework이다.

<!--more-->

# 용어 설명

## POM

"Project Object Model"

Maven의 Dependencies를 모두 여기서 관리한다.

parent tag : 이곳에서 dependency들의 version 기준을 정해준다. spring boot parent 1.4.0이라고 했다면

spring starter web을 dependency에 추가했을 경우 1.4.0에 해당하는 starter web과 함께 starter web에 필요한 다른 dependency도 자동으로 받아온다.

# About Spring

## Component Scan

Spring은 아래의 Annotation이 붙은 class들을 scan후 bean으로 등록한다.

@Repositoroy, @Component, @Controller(@RestController), @Service

이때 main class에 @Componentscan("path")를 지정해주어야만 해당 path를 scan한다. 보통은 project의 root path를 scan하도록한다.