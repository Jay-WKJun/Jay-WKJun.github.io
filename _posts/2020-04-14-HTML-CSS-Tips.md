---
layout: post
title: HTML CSS Tips
tags: [HTML, CSS]
excerpt_separator: <!--more-->
---

# summary

 여기선 HTML과 CSS의 활용 Tip들을 적어놓는다.
 
 대략적인 웹의 layout을 이쁘게 만드는 방법들을 적어 놓습니다.
 
 <!--more-->
 
 # Frontend Reference
 
 https://webzz.tistory.com/221
 
 # inline과 block의 개념
 
 inline은 text라고 생각해서 옆에 다른 content가 와도 괜찮다.
 
 block은 box라고 생각해서 하나의 가로 공간을 모두 차지한다.
 
 # div에 float을 적용했을 떄
 
 div에 float을 적용하면 float가 적용된 block이 float이 적용 안된 block에게 height값이 0으로 인식되어 버려서 프레임이 깨져버린다.
 
 해결방법 
 
 1. 깨지는 box에 가서 똑같이 float을 적용해준다.(잘 안씀)
 
 # 짜잘 Tip
 
 여러 tip들을 모아놓음
 
 ## 가운데 정렬
 
 margin: 0 auto  (위 아래는 0의 여백, 좌우는 자동으로 여백을 주어 가운데 정렬 하라는 뜻이다.)
 
 line-height: height px (한줄일때!!, height값과 동일 값을 넣어주면 가운데 정렬이 된다.)