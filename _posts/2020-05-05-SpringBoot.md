---
layout: post
title: Mixin repository
tags: [FrameWork, Java, Spring, SpringBoot]
excerpt_separator: <!--more-->
---

# Spring Boot

Spring Boot는 Spring의 설정들을 간편하게 설정할 수 있도록 나온 spring framework의 파생형 framework이다.

<!--more-->

# 용어 설명 And Thories

## POM

"Project Object Model"

Maven의 Dependencies를 모두 여기서 관리한다.

parent tag : 이곳에서 dependency들의 version 기준을 정해준다. spring boot parent 1.4.0이라고 했다면

spring starter web을 dependency에 추가했을 경우 1.4.0에 해당하는 starter web과 함께 starter web에 필요한 다른 dependency도 자동으로 받아온다.

## What is REST?

a.k.a Representational State Transfer

Definition = Architectural style for the web. REST specifies a set of constraints.

- Client - Server : Server (service provider) should be different from a client (service consumer). Enables loose coupling and independent evolution of server and client as new technologies emerge.
- Each service should be stateless.
- Each Resource has a resource identifier.
- It should be possible to cache response.
- Consumer of the service may not have a direct connection to the Service Provider. Response might be sent from a middle layer cache.
- A resource can have multiple representations. Resource can modified through a message in any of the these representations.

## Request Methods

- GET - Retrieve details of a resource
- POST - Create a new resource
- PUT - Update an existing resource
- PATCH - Update part of a resource
- DELETE - Delete a resource

## Content Negotiation

client(service requester)와 server(service provider)가 서로 소통하는 것으로 내가 받을 response와 server가 제공할 수 있는 request를 조율하고 server는 client의 request에 맞춰 response를 주는 negotiation같은 통신의 개념이다.

여기서 406 error의 경우 "I cannot respond with your request"로 server가 client가 요청하는 데이터 형식에 respond할 수 없다는 에러이다.

# About Spring

## @Component Scan

Spring은 아래의 Annotation이 붙은 class들을 scan후 bean으로 등록한다.

@Repositoroy, @Component, @Controller(@RestController), @Service

이때 main class에 @Componentscan("path")를 지정해주어야만 해당 path를 scan한다. 보통은 project의 root path를 scan하도록한다.

## starter parent

spring boot 에는 여러 spring boot starter xxxx 들이 많은데 spring boot starter parent를 pon.xml에서 설정해주면

Dependency versioning

Default plugins

Java version

을 통일해준다.

## Auto Configuration

Auto Configuration은 spring boot만의 특징적인 기능으로 spring에서 직접 등록해야했던 bean들을 자동으로 가져오도록 하는 기능이다.

이미 검증된 기능들의 집합이기 때문에 이 모든 기능들이 서로 compatible하다.

사용방법 : main class에 @EnableAutoConfiguration을 붙여줘야 사용이 가능하다.

각 AutoConfigruation들은 필요한 상황에만 자신이 실행될 수 있도록 @Conditional, @Condition과 같은 annotation들로 설정이 되어있다.

그 annotation 을 기반으로 필터링이 먼저 이뤄지고 필터링되지 않은 AutoConfigruation을 가지고 작업이 진행된다.

## Debuging

Auto configuration을 debug하는 방법이 있는데

src/main/resources/application.properties 파일에 logging.level.org.springframework: DEBUG

를 입력하고 실행하면 console에 auto configuration된 파일들을 debug한다. 각 파일들의 debug결과가 console에 출력된다.

## customized dynamic configuration

기존의 configuration을 override 했던것 처럼 내가 직접 작성하고 설정하는 configuration도 추가가 가능하다

 방법

- name : "content" -> @Value("${name}") annotation 추가해서 Object선언(Autowired와 같은 기능이다.)
- program argument에서 name="content" (argument가 properties보다 우선순위가 상위이다)
- spring.config.location=classpath:/default.properties -> 즉 새로운 properties file을 만들어 추가한다.
- placeholder를 사용한다.(name:"content"선언 후 ${name}으로 다른 configuration에 추가해준다.)
- YAML로 properties의 내용을 대체할 수 있다.

## Profile 기능

profile을 통해서 여러가지 properties 파일을 생성해놓고 필요에 따라 불러내주어 사용할 수 있다.

profile Setting

1. Using -Dspring.profiles.active=prod in VM Arguments
2. spring.profiles.active=prod

Using a profile (위에서 사용하겠다고 설정해준 profile만을 사용하게 된다. properties, bean도 마찬가지)

- application-{profile-name}.properties
- @Profile("profile-name") on a bean

Usage (하나의 service를 property만 바꾸어 사용하는 곳 어디서든 사용이 가능하다)

Configure Resources - Databases, Queues, External Services

## Advanced Application Configuration

Application Configuration을 YAML이나 .properties에 설정해 준 것을 Object를 통해 Bean으로 만들 수 있다.

Type Safe이기 때문에 해당 value의 형태가 맞지 않다면 오류가 발생한다.

ex) 만약 properties파일이 이렇게 설정 되어 있다면

```yaml
basic:
    value:true
    message:Welcome it's configuration Bean
    number:200
```

Configuration을 받아올 Object를 만들어 @Component를 통해 이렇게 불러 올 수 있다.

```java
@Component
@ConfigurationProperties("basic")
public class BasicConfiguration{
    //basic.value의 값을 가져온다.
    private boolean value;
    //basic.message의 값을 가져온다.
    private String message;
    //basic.number의 값을 가져온다.
    private int number;

    //gettter, setter 생략
}
```

@Value("address")로도 가능하지만 이런식으로 Object를 통해 configuration value를 통째로 bean으로 만들수도 있다.

# spring boot plugins

## jackson databind

 default plugin 중에 하나, messsage converter로 HTML에 출력되거나 거꾸로 데이터를 받아 올때 Object -> JSON JSON ->Object의 기능을 한다.

## spring developer tools

얘가 없다면 console을 통해 server를 끄고 다시 실행해서 바뀐 것을 확인해야하지만

dev tool을 통해 무언가 바뀐게 있다면 dynamic하게 적용하여 browser상에 적용해준다.

추가 설명...

- By default, any entry on the classpath that points to a folder will be monitored for changes.
- These will not trigger restart - /META-INF/maven, /META-INF/resources ,/resources ,/static ,/public or /templates
- Folders can be configured : spring.devtools.restart.exclude=static/,public/
- Additional Paths : spring.devtools.restart.additional-paths(needed to be added in configuration)

## spring boot actuator & HAL browser

actuator - monitoring application for my REST service

/env(실행환경확인), /metrics(memory와 연산처리 등의 정보), /trace, /dump, /shutdown, /beans(spring이 저장중인 모든 bean들 보기), / autoconfig(autoconfigure된 api들), /configprops, /mappings

HAL browser - actuator의 UI

http://localhost:8080/actuator/

여러 configuration을 보고 application.properties에서 override해서 설정을 바꿀 수도 있다.

## embedded servlet container

원래는 war를 만들어서 Tomcat을 통해 배포해야하지만 spring boot에 내장된 servlet container와 Tomcat app server를 이용해 그렇게 하지 않아도 가능하다. war 대신 jar를 써서 web server를 여는것이다.

## JPA & h2 DataBase

JPA는 DB와 통신하는 Repository의 기능을 가진 api이다. DataBase가 없이는 단독으로 사용이 불가능해서 보통 h2와 같이 쓰인다.

h2 DataBase는 in memory DataBase로 프로그램이 켜지면 작동하고 꺼지면 모든 DB가 없어진다는게 특징이다. 처음 설정이 필요없어서 초보자들에게 연습용으로 유용하다. h2는 자신의 UI도 있다. 이 주소에서 console을 이용할 수 있다. [http://localhost:8080/h2-console]