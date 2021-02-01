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

![flexBoxConcept]({{ "/assets/img/aboutCSS/flexBoxConcept.PNG" | relative_url }})

FlexBox의 concept

![flexBoxPropertyOverview]({{ "/assets/img/aboutCSS/flexBoxPropertyOverview.PNG" | relative_url }})

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

![conceptOfGrid]({{ "/assets/img/aboutCSS/conceptOfGrid.PNG" | relative_url }})

![GridOverview]({{ "/assets/img/aboutCSS/GridOverview.PNG" | relative_url }})

## grid basic setting

```css
.container{
    display: grid;
    /*둘 다 같은 방식*/
    grid-template-rows: 150px 150px;
    grid-template-rows: repeat(2, 150px);
    /*둘 다 같은 방식*/
    grid-template-columns: 150px 150px 150px;
    grid-template-columns: repeat(3, 150px);
    /*모두 같은 방식*/
    grid-row-gap: 30px;
    grid-column-gap: 30px;
    grid-gap: 30px;
}
```

## fr unit

fractional unit이라는 뜻으로 최대 채울 수 있는 크기 만큼 채워주는 단위이다.

(해당 item의 fr * (전체 length/전체 fr))

```css
.container{
    /*하나는 150px 다른 하나는 채울 수 있을 만큼의 크기를 가진다.*/
    grid-template-rows: repeat(1, 150px) 1fr;
    /*2fr은 1fr보다 2배 크다*/
    grid-template-columns: 1fr 2fr 1fr;
}
```

## Positioning & Spanning

item들의 positioning과 spanning

```scss
.container{
    .item{
        &--1{
            /*2번~3번줄 2번~3번열 에 위치시킨다.*/
            /*positioning 방법1*/
            grid-row-start: 2;
            grid-row-end: 3;
            grid-column-start: 2;
            grid-column-end: 3;
            /*positioning 방법2*/
            grid-row: 2 / 3;
            grid-column: 2 / 3;
            /*positioning 방법3*/
            grid-area: 2 / 3 / 2 / 3;
        }
        &--2{
            /*spanning
            첫번째줄 1,2번째 열 공간을 차지한다.
            */
            grid-row: 1 / 2;

            grid-column: 1 / 3;
            grid-column: 1 / span 2;
        }
        &--3{
            /*spanning법칙
            1. 알아서 비켜줌
            --2가 같은 공간을 차지하고 있지만
            --3은 row가 명시적이지 않기 때문에 1번째 colulmn중에 빈공간을 찾거나 새로운 row를 만들어 그곳을 차지한다.
            */
            grid-column: 1 / 2;
        }
        &--4{
            /*spanning법칙
            2. 겹침
            --1과 같은 공간을 차지하고 
            둘 다 명시적으로 공간을 차지한다.
            z-index로 드러내고싶은 item을 정할 수 있다.
            */
            grid-row: 2 / 3;
            grid-column: 2 / 3;
        }
        &--5{
            
        }
        &--6{
            
        }
    }
}
```

spnning법칙은 명시적인 것이 항상 우선순위를 가진다.

## naming

Grid는 자동으로 붙여지는 줄번호가 있어 그것을 사용할 수 있지만 우리가 이름을 붙여줄 수 있다.

Grid-line과 Grid-Area는 naming이 가능하다.

- Grid-line

```css
/*Grid에 생성되는 라인에 이름을 각각 붙여준다. 띄어쓰면 이름을 여러개 줄 수 있다.*/
.container{
    grid-template-rows: [header-start] 100px [header-end box-start] 200px [box-end main-start] 400px [main-end footer-start] 100px [footer-end];
    /*repeat시엔 col-start 1, col-start 2, col-start 3 처럼 연속된 number가 붙는다.*/
    grid-template-columns: repeat(3, [col-start] 1fr [col-end]) 200px [grid-end]; 
}

.header{
    /*사용할 땐 line number대신 이렇게 사용*/
    grid-row : header-start / header-end;
    grid-column: col-start 1 / col-end 3;
}
```

- grid-area

item별로 이름을 지정한다. 모든 item에 직접 이름을 설정해줘야되서 큰 gird에선 비효율적이다.

```css
.container{
    grid-template-rows: 100px 200px 400px 100px;
    grid-template-columns: repeat(3, 3fr) 200px;
    /*위에서 16개의 item이 나오도록 설정했으므로
    area 이름도 정확히 16개가 되어야 한다.
    .으로 빈 공간을 만들어 줄 수 있다.*/
    grid-template-areas: "head head head ."
                        "box box box side"
                        "main main main side"
                        "foot foot foot foot"
}
.header{
    /*head에 해당하는 grid item area에 적용된다.*/
    grid-area: head;
}
.sider{
    grid-area: side;
}
```

## implicit Grid

명시적인 grid범위를 넘어선 item이 있을 경우 알아서 grid를 추가해 item을 정렬해 넣는데 default는 row로 개행해서 추가해준다.

```css
.container{
    /*이렇게 설정하면 초과한 item들이 오른쪽에 정렬되어 추가된다.*/
    grid-auto-flow:column;
    /*이렇게 자동으로 추가된 grid의 크기도 설정할 수 있다.
    .5fr은 5fr의 half를 뜻한다*/
    grid-auto-columns: .5fr;
}
```

## Aligning

정렬엔 item정렬과 Grid Track 정렬이 있다.

```css
/*
justify는 x축
align은 y축

item은 item의 align
: 할당된 item공간안에서 item을 정렬한다.
(self로 각 개별 item의 정렬을 override할 수 있다.)
content는 track의 align
: 전체 container 공간 안에서 Grid track을 정렬한다.
*/

.container{
    justify-items: center / Stretch / end / start;
    align-items: center / Stretch / end / start;

    justify-content: center / start / end / space-between / space-around / space-evenly;
    align-content: center / start / end / space-between / space-around / space-evenly;
}
```

## min-content max-content minmax()

- min-content

item의 content중에 가장 최소한의 length만을 가진다.

("max apple" 이 있다면 개행시키고 apple만큼의 length를 가진다.)

- max-content

item의 content중에 가장 최대한의 length를 가진다.

("max apple" 이 있다면 개행하지 않은 최대의 length를 가진다.)

- minmax(min max)

length가 min과 max사이에 있도록 설정한다.

## auto-fit auto-fill

- auto-fill

```css
.container{
    width: 1000px;
    display: grid;
    /*100px 짜리 10개 열을 채워준다. (보통은 정해진 갯수만 채우고 끝)*/
    grid-template-columns: repeat(auto-fill, 100px);
}
```

- auto-fit

반응형 개발에 매우매우매우매우 중요한 기능이다.

```css
.container{
    width: 1000px;
    display: grid;
    /*item갯수와 주어진 크기에 맞추어 알아서 grid 열을 만들어준다.*/
    grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
}
```

1fr로 화면을 꽉채운 상태에서 화면을 줄이면 100px가 되었을 때, 개행하여 새로운 줄에서 auto-fit하여 깔끔하게 만들어준다.

따라서 이것 하나면 어떠한 화면 크기에서도 원하는 display형태를 얻을 수 있다.