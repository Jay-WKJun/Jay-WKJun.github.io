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
 
 inline-block이라고 하는 복합속성도 존재한다.
 
 ```css
 /* 헤더 */
.header {}
/* header의 자식인 header-menu의 자식인 a tag, 부모자식 관계는 한칸씩 띄워서 표현(웬만하면 그냥쓰지말고 표현해주자) */
.header .header-menu {text-align: right;}
.header .header-menu a {color: #fff; padding: 10px 0px 10px 13px; display: inline-block;}
.header .header-menu a:hover {color: #666;}
```

a tag는 inline속성이기 때문에 자기 자신만이 그 자체의 영역이라 padding은 가로로만, text-align도 먹히지 않는다. (자기 자신이 영역이기 때문에)

이때, display: inline-block 속성을 주게 되면 inline 속성을 유지하면서 block 속성을 가지기 때문에, 한 block안에 여러 inline이 있는 것 처럼 행동해서

여러 a tag가 한가로줄안에 정렬되고 각각의 a tag는 padding 속성도 가질 수 있게 된다.(block만 주면 하나의 a tag가 하나의 가로줄을 점유한다.)

## ex2

아래 header title h1은 원래 block속성으로 container의 가로 한줄을 모두 차지했지만, display: inline-block;을 주어 background영역을 자기 자신의 글자만큼만 제한하고 위치 정렬까지 했다.

```css
.header .header-tit h1 {color: #fff; background-color: #4aa8d4; display: inline-block; font-size: 28px; padding: 5px 20px 6px 20px; margin-top: 40px; font-weight: normal; text-transform: uppercase;}
.header .header-tit a {display: inline-block; background-color: #2698cb; font-size: 18px; color:#fff; padding: 5px 20px 6px 20px; margin-top: -5px;}
```

 # skip navigation
 
 ``` css
 /* 스킵 내비게이션 */
#skip {position: relative;}
#skip a {position: absolute; left: 0px; top: -35px; width: 140px; border:1px solid #fff; color: #fff; text-align: center; background: #333; line-height: 30px;}
#skip a:active, 
#skip a:focus {top: 0;}
 ```
 
 css Tip : css에서 같은 속성에 여러가지 조건을 달고 싶다면 ','를 사용해서 뒤에 이어 쓰면 된다. active와 focus처럼
 
 ```html
 <!-- 스킵 내비게이션 -->
    <div id="skip">
        <a rel="bookmark" href="#cont_nav">전체 메뉴 바로가기</a>
        <a rel="bookmark" href="#cont_ban">배너 영역 바로가기</a>
        <a rel="bookmark" href="#cont_cont">컨텐츠 영역 바로가기</a>
    </div>
<!-- //스킵 내비게이션 -->
 ```
 
 id가 skip인 div에 a tag의 bookmark를 넣고 각각을 해당 div의 id와 연결해준다.
 
 각각의 a tag는 처음엔 -35px로 화면밖에 있다가 tap으로 active되면 focus되어 top이 0으로 변해서 화면 왼쪽위에 나타나게 된다.
 
 # image를 표현하는 방법

img는 의미가 있는것들(필수), background는 의미가 없는 것들로 나누어 표현해준다.

만약 적절한 img가 없다면 alt tag를 통해 적절한 text를 제공해야한다.(by. web standard)

But!!! 최근 많은 web site들은 image sprite라는 기법을 이용해 image를 background 처리하고 가상으로 alt text를 제공한다.(aka. "IR 기법")

(image sprite : 사용하고 싶은 icon 등을 모두 하나의 image에 넣고 background에 넣은 다음에 필요한 위치에 필요한 image를 pixel단위로 잘라서 보여주는 기법이다. 하나의 image만 관리하면 된다는 장점이 있다.)

만약 img tag를 일일히 넣어줘야한다면 image들을 모두 쪼개서 주소값(<img src=)을 줘야하고, alt text(<img alt=)까지 모두 줘야해서 매우 번거로워 진다.

 ## IR기법

가상으로 al text를 제공하기 위한 방법으로 여러가지 종류의 IR기법이 있다. [http://darum.daum.net/convention/css/css_ir]
 
css를 이용해 원래 있는 text를 안보이게 하는 방법으로 해당 text는 존재하기 때문에 대체 text이고 그 자리에 background image가 오게 된다.

```css
/* IR 효과 */
.ir_pm {display:block; overflow:hidden; font-size:0; line-height:0; text-indent:-9999px;} /* 의미있는 이미지의 대체 텍스트를 제공하는 경우(Phark Method) */
.ir_wa {display:block; overflow:hidden; position:relative; z-index:-1; width:100%; height: 100%;} /* 의미있는 이미지의 대체 텍스트로 이미지가 없어도 대체 텍스트를 보여주고자 할 때(WA IR) */
.ir_su {overflow: hidden; position:absolute; width:0; height:0; line-height:0; text-indent:-9999px;} /* 대체 텍스트가 아닌 접근성을 위한 숨김 텍스트를 제공할 때 */

/* style에 적용 , background-position의 px를 이용해 필요한 그림만 가져온다.*/
.header .header-icon a {width: 60px; height: 60px; display: inline-block; background: url(img/icon.png); margin: 0 3px;}
.header .header-icon a.icon1 {background-position: 0px 0px;}
.header .header-icon a.icon2 {background-position: 0px -60px;}
.header .header-icon a.icon3 {background-position: 0px -120px;}
.header .header-icon a.icon4 {background-position: 0px -180px;}
/* mouse over하면 다른 그림이 뜨도록 한다. */
.header .header-icon a:hover.icon1 {background-position: -60px 0px;}
.header .header-icon a:hover.icon2 {background-position: -60px -60px;}
.header .header-icon a:hover.icon3 {background-position: -60px -120px;}
.header .header-icon a:hover.icon4 {background-position: -60px -180px;}
```

```html
<!-- span class에 걸린 css를 통해 텍스트 안보이게 한다.(안보이기만 할 뿐 대체 텍스트를 제공하는 것이다.) -->
<a href="#" class="icon1"><span class="ir_pm">icon1</span></a>
<a href="#" class="icon2"><span class="ir_pm">icon2</span></a>
<a href="#" class="icon3"><span class="ir_pm">icon3</span></a>
<a href="#" class="icon4"><span class="ir_pm">icon4</span></a>
```
 
 # 짜잘 Tip
 
 여러 tip들을 모아놓음
 
 width의 default는 100%이다.
 
 ## div에 float을 적용했을 때의 영역깨짐
 
 div에 float을 적용하면 float가 적용된 block이 float이 적용 안된 block에게 height값이 0으로 인식되어 버려서 프레임이 깨져버린다.
 
 해결방법 
 
 1. (x)깨지는 box에 가서 똑같이 float을 적용해준다.(잘 안씀)(float를 적용해준 이후의 모든 block에 똑같이 float을 적용해줘야 하기 때문)
 2. (x)clear: both; 를 써준다. float의 속성을 상쇄하기 위해 나온 기능이다. -> 이건 어떤 영역이 깨졌는지 알기 어려워서 잘 안씀...
 3. (o)float를 사용한 상위 박스한테 overflow: hidden을 사용한다.(overflow: hidden = 내가 지정한 영역이외에는 안보이게 하는 것.)
 4. (o)claerfix를 사용함.(반응형에서 자주 사용한다.)
 
 ## 부모 자식관계
 
 자식이 만약 height값이 있고 부모도 그것을 공유하고 싶다면 부모에 굳이 height를 써주지 않아도 똑같이 적용된다.
 
 부모의 css정보를 자식이 공유하고 싶다면 inherit이라 써주면 된다. ex) height : inherit
 
 in css...
 
 css에서도 부모와 자식의 관계를 보여줄 수 있다. 부모를 지정하고 한칸 띄워서 그 안의 자식을 지정하면 된다.
 
 ## 가운데 정렬
 
 margin: 0 auto  (위 아래는 0의 여백, 좌우는 자동으로 여백을 주어 가운데 정렬 하라는 뜻이다.)
 
 line-height: height px (한줄일때!!, height값과 동일 값을 넣어주면 가운데 정렬이 된다.)(실제로 잘 쓰진 않는다.)
 
 ## web standard
 
 web을 surfing하는데 있어서 사람들의 불편함이 없도록 하기 위해 만든 표준 규정으로 이 규정을 지켜서 web page를 만들어야 하며 naver나 daum에도 깔끔하게 정리가 되어있다.
 
 ## code
 
 &는 code에서 쓰이기 때문에 저런식으로 &를 문자로 표현해달라는 식을 따로 넣어줘야한다.(안해도 상관없지만 자칫 문제를 일으 킬 수 있기 때문에 웬만하면 넣어주자.)
 
 ```html
 &amp;
```

## font 초기화

windows와 mac의 기본 font는 다르기 때문에 default font를 쓰게 된다면 frame이 깨질 수 있다. 따라서 공통인 web font를 이용 할 수 있다.

보통 Google font 사이트를 이용한다. [https://fonts.google.com/]

아래 font-family를 이용해 왼쪽부터 있는 것을 적용하고 없다면 오른쪽으로 넘어가게 된다. 'Nanum Gothic'이 Google font에서 가져온 것으로 없다면 html에 link가 걸려있기 때문에 알아서 다운받아 적용한다.

```css
/* 폰트 초기화 */
body, input, textarea, select, button, table {
    font-family: 'Nanum Gothic', AppleSDGothicNeo-Regular,'Malgun Gothic','맑은 고딕',dotum,'돋움',sans-serif; 
    color: #222; font-size: 13px; line-height: 1.5;}
```

```html
<!-- 웹 폰트 -->
<link href="https://fonts.googleapis.com/css?family=Nanum+Gothic" rel="stylesheet">
```

## 불릿 기호 초기화

```css
/* 블릿기호 초기화 */
dl,ul,li,ol,menu {list-style:none;}
```

## ablsolute와 relative 위치조절

position: absolute를 주면 block의 영역을 모두 무시하고 browser를 기준으로 잡고 위치를 조절하게 된다.

이때, 부모 block에 position: relative를 주면 부모 block이 absollute의 기준 역할을 하게 되어 부모 block안에서의 위치조절이 가능하다.

```css
/* 타이틀 */
.tit {position: relative; text-align: center}
.tit h2 {font-size: 40px; color:#2c94c4; letter-spacing: 2px; padding: 5px 0; font-family: 'Nanum Brush Script', cursive;}
.tit .btn {display: block; width: 60px; height: 60px; background: url(img/icon.png) no-repeat 0 -600px; position: absolute; right: 0; top: 5px;}
```

## 복수 이름 지정

아래의 html에서 column colX 라고 되어있는데 이는 class의 이름이 2개인 것이다.

따라서 .column이로 지정했을 땐 class이름이 column이라고 되어 있는 모두에게 적용이 되고 .colX는 각자의 column에 적용된다.

```css
/* 컨텐츠 박스 요소 width = padding + margin + border */
.cont {overflow: hidden; padding-top: 30px;}
.cont .column {position: relative; float: left; width: 289px; height: 363px; margin: 0 30px 30px 0; padding-right: 30px;}
.cont .col1 {border-right: 1px solid #c8c8c8;}
.cont .col2 {border-right: 1px solid #c8c8c8;}
.cont .col3 {margin-right: 0; padding-right: 0;}
.cont .col4 {border-right: 1px solid #c8c8c8;}
.cont .col5 {border-right: 1px solid #c8c8c8;}
.cont .col6 {margin-right: 0; padding-right: 0;}
```

```html
<div class="cont">
                        <div class="column col1">
                            <h3><span class="ico_img ir_pm">아이콘1</span><em class="ico_tit">Notice</em></h3>
                            <p class="ico_desc">가장 웹 페이지에서 기본이 되는 게시판 유형입니다.</p>
                        </div>
                        <!-- //col1 -->
                        <div class="column col2">
                            <h3><span class="ico_img ir_pm">아이콘2</span><em class="ico_tit">Notice</em></h3>
                            <p class="ico_desc">가장 웹 페이지에서 기본이 되는 게시판 유형입니다.</p>
                        </div>
                        <!-- //col2 -->
                        <div class="column col3">
                            <h3><span class="ico_img ir_pm">아이콘3</span><em class="ico_tit">Notice</em></h3>
                            <p class="ico_desc">가장 웹 페이지에서 기본이 되는 게시판 유형입니다.</p>
                        </div>
                        <!-- //col3 -->
                        <div class="column col4">
                            <h3><span class="ico_img ir_pm">아이콘4</span><em class="ico_tit">Notice</em></h3>
                            <p class="ico_desc">가장 웹 페이지에서 기본이 되는 게시판 유형입니다.</p>
                        </div>
                        <!-- //col4 -->
                        <div class="column col5">
                            <h3><span class="ico_img ir_pm">아이콘5</span><em class="ico_tit">Notice</em></h3>
                            <p class="ico_desc">가장 웹 페이지에서 기본이 되는 게시판 유형입니다.</p>
                        </div>
                        <!-- //col5 -->
                        <div class="column col6">
                            <h3><span class="ico_img ir_pm">아이콘6</span><em class="ico_tit">Notice</em></h3>
                            <p class="ico_desc">가장 웹 페이지에서 기본이 되는 게시판 유형입니다.</p>
                        </div>
</div>
```
## margin과 padding의 차이점

margin은 block간의 여백을 주고 padding은 내부 구성품들 간의 여백을 준다.

margin을 줄 경우 하얀 여백이 생기는 경우가 있다.

## 컨텐츠 요소를 보이지 않게 하는 방법

1. display: none (영역이 사라짐)
2. visibility: hidden; -- visiblity: visible; (영역 유지)
3. opacity: 0; -- opacity: 1; (영역 유지)
4. width: 0; height:0; overflow:hidden;

## marginal-right(left, bottom, top)를 -1로 해야되는 상황은?

border의 2줄 효과를 한줄로 보이게 할 때 (서로 겹쳐서 1줄로 보이게 된다.)

(신입이 알고있다면 조금은 놀라운 지식이다.)

## text의 한줄 효과

```css
overflow: hidden; text-overflow: ellipsis; white-space: nowrap;
```

## 가상선을 이용하는 방법

글자들을 구분해주는 가로선이나 세로선을 줄 때 그림이 아닌 css를 이용해 가상으로 선을 넣어줄 수 있다.

```css
/* li의 앞에 content는 넣지말고 backgraound로 색, width랑 height로 선 굵기, 길이를 정하고 위치를 정해준다음 적용한다. */
.footer li:before {content: ''; width: 1px; height: 12px; background: #ccc; position: absolute; left: 0; top: 2px;}
/*맨 첫번째 것은 생략하기 위해 추가한다.*/
.footer li:first-child:before {width: 0; height: 0;}
```