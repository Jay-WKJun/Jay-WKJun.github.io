---
layout: post
title: How CSS works
tags: [HTTP, CSS, principle]
excerpt_separator: <!--more-->
---

# How CSS works

CSS의 작동 원리에 대해서 알아본다.

<!--more-->

# When its Load up

![WhenCSSLoadedUp]({{ "/assets/img/aboutCSS/WhenCSSLoaded.PNG" | relative_url }})

# How CSS is Parsed

## The Cascade and Specificity

css가 적용되는 우선순위

![CSSCascadeAndSpecificity]({{ "/assets/img/aboutCSS/CSSCascadeAndSpecificity.PNG" | relative_url }})

user가 가지고 있는 개별 css정보

author는 내가 써서 제공하는 css정보

![CSSTip]({{ "/assets/img/aboutCSS/CSSTip.PNG" | relative_url }})

```html
<nav id="nav">
    <div class="pull-right">
        <a class="button button-danger" href="link.html">Don't Click here!</a>
    </div>
</nav>
```

```css
/*specificity의 우선순위가 높을수록 갯수가 많을 수록 적용 우선순위 UP!*/

/*inline IDs Classes Elements*/
body {
    padding: 50px;
}
.button{
    fons-size: 20px;
    color: white;
    background-color: blue;
}/*(0,0,1,0)*/
a {
    background-color:purple;
}/*(0,0,0,1)*/
#nav div.pull-right a.button{
    background-color:orange;
}/*(0,1,2,2)*/
#nav a.button:hover{
    background-color:yellow;
}/*(0,1,2,1)*/

/*결과는 orange*/
```

hover에 관해

hover하는 순간 Element에 하나가 더 추가 되어 specificify 우선순위가 변하여 가장 우선순위가 높아지면 해당 css가 적용된다.

# How CSS values are processed

모든 크기 단위는 결국 pixel로 변환되어 적용된다.

![CSSValueCalculation]({{ "/assets/img/aboutCSS/CSSValueCalculation.PNG" | relative_url }})

- em, rem

***주의!!!!  em은 font에 적용시 parent의 font-size, length에 적용시 자신의 font-size를 기준으로 계산한다!

rem은 document(root)에 적용된 font-size를 기준으로 계산한다.

font-size의 default = 16px

- vh, vw

viewport는 사용자가 보는 browser의 창의 크기를 말하며 그것을 기준으로 한다.

# inheritance

헷갈리기 쉬운 inheritance 법칙 적용

## computed Value

```css
.parent{
    font-size: 20px;
    line-height: 150%;
}
.child{
    font-size: 25px;
}
```

여기서 .child는 line-height를 지정해주지 않았기에 .parent에게 inherit받는다.

inherit시에는 20px * 150% = 30px로 적용되어 온다.

(<u>상대적인 선언 값들은 모두 이미 계산된 수치가 inherit된다.</u> 즉, 150%가 inherit되어 25px * 150%가 되는 것이 아니다.)

## inherit되는 종류

- text와 관련된 것들은 모두 inherit된다.
- margin, padding같은 value는 전혀 inherit되지 않는다.

# How to Render

## box model

![boxmodel]({{ "/assets/img/aboutCSS/boxmodel.PNG" | relative_url }})

여기서 box-sizing! 이란?

width와 height는 content의 pixel을 정하기 때문에 total length에 영향을 줄 뿐 control하기 어렵다

따라서

![boxSizing]({{ "/assets/img/aboutCSS/boxSizing.PNG" | relative_url }})

box-sizing이라는 것을 사용해 total length를 조절할 수 있다.

## inline, block, inline-block

![BlockTypes]({{ "/assets/img/aboutCSS/BlockTypes.PNG" | relative_url }})

{ display: inline; } : 대표적으로 <span>이라는 태그의 성질로 content/text 크기만큼만 점유하고 동일 라인에 붙는다.
'이 글씨는 두꺼운 효과를 주었다.'와 같이 text 내에 특정 부분에만 스타일을 간단히 줄때 많이 사용

{ display: block; } : 
반면 block은 무조건 한줄을 점유하고, 다음 태그는 다음 줄 개행.

## position : float

float 은 한 요소(element)가 보통 흐름(normal flow)으로부터 빠져 텍스트 및 인라인(inline) 요소가 그 주위를 감싸는 자기 컨테이너의 좌우측을 따라 배치되어야 함을 지정한다.

 float:left    - 요소를 왼쪽 방향으로 띄워지게 지정. y축의 값을 x축으로 옮겨준다. 

 float:right  - 요소를 오른쪽 방향으로 띄워지게 지정. 오른쪽이 기준이 됨.

 float:none  - 기본값, 요소를 띄우지 않음. 텍스트가 위치하는 곳에 위치.

# BEM naming system

stands for "Block Element Modifier"

BEM 방법론은 ID에는 사용할 수 없고, 오직 클래스명에만 활용할 수 있다는 점에 주의!!

class="Block__Element--Modifier"

블럭(block)은 문단 전체에 적용된 요소 또는 요소를 담고 있는 컨테이너

요소(element)는 블럭이 포함하고 있는 한 조각

modifier은 블럭 또는 요소의 속성, 블럭 또는 요소의 외관이나 상태를 변화시키는 것

ref : https://webclub.tistory.com/263