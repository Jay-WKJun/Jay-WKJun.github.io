---
layout: post
title: FlexBox and Grids
tags: [HTTP, CSS, flexbox, grid]
excerpt_separator: <!--more-->
---

# FlexBox and Grids

CSS3에 추가된 최신 정렬기능으로 간편하고 깔끔하게 요소들을 정렬할 수 있다.

FlexBox : 1차원(가로, 세로)공간에 요소들을 정렬하는 기능

Grids : 특정 2차원 공간에 요소들을 정렬하는 기능

<!--more-->

# FlexBox

직선(가로, 세로)공간에 요소들을 정렬하는 기능

기존의 float를 대체한다

말그대로 flex하기 때문에 '반응형' 디자인에 유용하다.

훨씬 적은 코드로도 반응형 디자인을 구현할 수 있어 가독성이 높고 보이는 디자인도 깔끔하다.

![flexBoxConcept]({{ "/assets/img/aboutCSS/flexBoxConcept.png" | relative_url }})

FlexBox의 concept

![flexBoxPropertyOverview]({{ "/assets/img/aboutCSS/flexBoxPropertyOverview.png" | relative_url }})

FlexBox의 Property

## justify-content, align-items

justify-content는 main axis를 기준으로 정렬방법을 정의하고,

align-items는 그 수직 axis를 기준으로 정렬방법을 정의한다.

## align-content

FlexBox 바깥에서의 정렬방법을 정의한다. 따라서 정의하면 flexBox가 통째로 움직인다.

## flex-wrap

item이 해당 row 혹은 column에서 최대한의 공간을 점유하고도 부족할 시, 줄바꿈을 할지 말지 설정해준다.

default: unwrap, wrap을 설정하면 item이 알아서 줄바꿈을 하여 다른 줄에서 자신이 가질 수 있는 최대 크기로 점유한다.

## align-self

align-items를 override한다.

## order

default는 0, 정렬 우선순위를 설정한다. 오름차순이다.

## flex-grow, -shrink, -basis

grow는 설정하면 item이 가질 수 있는 최대 공간을 모두 점유한다. 

default: 0, item끼리의 상대적인 값으로 item1이 1이고 item2가 2면 item2는 item2의 두배의 크기가 된다.

basis는 직접 length를 설정해 item의 크기를 설정한다.

default: auto

shrink는 view port가 줄어 해당 item의 점유할 수 있는 공간이 줄어들 시에 줄어들지 말지 결정한다.

default: 0, 1로 설정하면 줄어들지 않고 화면크기가 작아지면 사라진다.

# Grids

특정 2차원 공간에 요소들을 정렬하는 기능

정확히 구획을 나누어주기 때문에 깔끔하게 정렬된다.

이것 또한 기본적으로 반응형이며 float를 대체할 수 있다.

