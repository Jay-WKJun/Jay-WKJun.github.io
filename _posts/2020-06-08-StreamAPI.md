---
layout: post
title: Stream Api
tags: [Java, Stream api, api, Functional programming]
excerpt_separator: <!--more-->
---

# Stream API

Java 8에서 추가된 기능으로, stream 형태의 요소에 함수형 연산자를 지원해주는 클래스이다.

<!--more-->

요약하자면 아래 그림과 같다.

![StreamAPI]({{ "/assets/img/IT_Pic/streamApiExplain.jpg" | relative_url }})

물고기들은 바닷속에서 헤엄치며 앞으로 나아가다가 어부가 놓은 그물에 잡히고, 일정한 규격의 상자에 담긴 후 한꺼번에 모아 최종 소비자에게 전달된다.

이를 stream과 대응시켜보자.

우선, 바다 생물들의 이동은 stream이다.

상품가치가 있는 물고기를 잡기위해 그물로 필터링 하는것은 filter라는 중간 연산자이고, 필터링 조건은 람다 함수 또는 메소드 참조를 통해 결정된다.

물고기를 상자에 담는 과정 또한 중간 연산자 중 하나로, map이라고 한다. 이 과정에서 물고기를 차량(컬렉션)에 적재하기 적당한 형태로 가공된다.

마지막으로, 물고기가 실린 수많은 상자를 차에 실어서 소비자에게 제공하는데, 이러한 행위가 collect라는 최종 연산자 이다.

이렇게 보니 간단하다. 수많은 데이터의 흐름 속에서 각각의 값을 원하는 형태로 가공하여 최종 소비자에게 제공하는 것이 stream의 역할이다.

# Stream

stream은 Array, Collections와 같이 연속된 형태의 객체이다. 하지만 자료구조는 아니다. 위와 같은 데이터를 입력으로 받아 메소드로 처리할 뿐이다.

stream은 입력받은 원래의 자료구조를 바꾸지는 못한다. 대신, 파이프라인 형태로 연결된 메소드의 결과를 제공한다. 원본 데이터를 바꾸지 못하는 특성 덕분에 side effect를 제거할 수 있다.

최종 연산자(terminal operation)는 stream의 끝을 의미하며 모든 연산자를 수행한 결과를 반환한다. 다시말해, 최종 연산자까지 모두 수행한 stream은 재활용 할 수 없다.

요약하자면, stream은 다음과 같은 형태로 결과를 만들어낸다.

```java
// 스트림 생성 -> 중간 연산자 -> 최종 연산자
int result = list.stream()  // 스트림 생성
        .filter( ... )      // 중간 연산자
        .map( ... )         // 중간 연산자
        .count();           // 최종 연산자
```

# Stream methods

Stream 에는 여러 연산자, 메소드들이 있다.

[https://velog.io/@kskim/Java-Stream-API]