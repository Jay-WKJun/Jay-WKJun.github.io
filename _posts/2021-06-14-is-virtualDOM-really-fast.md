---
layout: post
title: Is VirtualDOM Fast???
tags: [React, VirtualDOM, Optimization]
excerpt_separator: <!--more-->
---

# Virtual DOM

React의 핵심 feature! Virtual DOM! 정말 기가막히게 빠른걸까??

<!--more-->

![React&VirtualDOM]({{ "/assets/img/aboutReact/reactAndVirtualDOM.jpg" | relative_url }})

# 실제론...

실제론 어떤 일부 DOM element를 한 번 변화시킨다고 했을 때,

Virtual DOM없이 DOM에 직접 접근해서 바꾸는게 훨씬 빠르다고한다.

### reference

[성능 Benchmark 자료](https://rawgit.com/krausest/js-framework-benchmark/master/webdriver-ts-results/table.html)

(위, Benchmark자료에 의하면 Vanilla Javascipt가 React보다 100배는 빠르다고 한다.)

[Virtual DOM is pure overhead, by SVELTE](https://svelte.dev/blog/virtual-dom-is-pure-overhead)

(SVELTE 공식 게재글이니 한번 읽어보면 좋을 것 같습니다)

[how exactly is reacts virtual dom faster, from stackoverflow](https://stackoverflow.com/questions/61245695/how-exactly-is-reacts-virtual-dom-faster)

(Exactly하게 설명된 답변이 있습니다.)

## 왜...?

React도 결국엔 Real DOM을 update해야한다. 그 과정에서 Virtual DOM이 끼기 때문이다.

간단히 수정과정을 도식화 해본 다면,

```
1. Vanilla javascript
DOM 수정 -> Render(reflow, repaint)

2. React
Virtual DOM 수정 -> Virtual DOM vs Virtual DOM (diffing) -> 바뀐 곳 Render(reflow, repaint)
```

그렇다...! Render전에 Virtual DOM의 수정과 Diffing 과정이 추가되기 때문이다.

### diffing algorithm reference

[](https://minemanemo.tistory.com/120)

[](https://yeoulcoding.tistory.com/147)

[](https://dev.to/koolkishan/what-is-virtual-dom-how-virtual-dom-works-what-is-reconciliation-what-is-diffing-algorithm-what-makes-react-so-fast-327a)

## 그럼에도 React

![whyReact]({{ "/assets/img/aboutReact/whyReact.png" | relative_url }})

<u>그렇다면 왜 React를 계속 사용할까??</u>

그 이유가 오늘 Post의 주제이다.

- **어떤 상황에서도 준수한 성능을 보여주기 때문이다.**

- **귀찮은 batching을 자동으로 해준다!**

# Virtual DOM

본격적으로 이유를 알아보기 전에 Virtual DOM이 무엇인지 알아보자!

Virtual DOM은 브라우저 화면에 그려지는 Real DOM을 추상화한 객체이다.

대략 이런식으로 생겼다.

```HTML
// Real DOM
<ul class="fruits">
    <li>Apple</li>
    <li>Orange</li>
    <li>Banana</li>
</ul>
```

```javascript
// Virtual DOM representation
{
  type: "ul",
  props: {
    "class": "fruits"
  },
  children: [
    {
      type: "li",
      props: null,
      children: [
        "Apple"
      ]
    },
    {
      type: "li",
      props: null,
      children: [
        "Orange"
      ]
    },
    {
      type: "li",
      props: null,
      children: [
        "Banana"
      ]
    }
  ]
}
```

## VDOM의 목적

VDOM의 목적은 **state의 변화를 감지하고 그것을 re-render하는데에 있다.**

이러한 Approach는 다른 framework에서 각기 다른 방법으로 접근했다.

VDOM은 state의 변화를 VDOM에 그대로 적용하고 그것을 Real DOM과 비교해 바꾸는 방식으로 접근했다.

### AngularJS의 "Dirty checking"

한편, AngularJS에선 state를 만들면 해당 state의 **"watcher"**라는 일종의 Detector도 같이 만들게 된다.

이제 app에서 어떤 일이 벌어지면 <u>모든 "watcher"를 탐색</u>하여 state의 변화를 감지하고 그것을 re-render한다.

이 때, 모든 "watcher"를 탐색하는 과정을 **"Dirty checking"**이라고 한다.

(여기서 state가 많아진다면...? ㅎㅎ)

# 준수한 성능

![Anywhere]({{ "/assets/img/gifs/tenorAnywhere.gif" | relative_url }})

You can use **AnyWhere!**

그렇다면 이제부터 본격적으로 Virtual DOM과 React의 장점에 대해 알아보자!

DOM을 갱신하는데 Virtual DOM이 끼어버리면 DOM 갱신이 느려지는건 당연하다...!

하지만 그 변화가 100개... 1000개가 된다면...?

<u>cost가 기하급수적</u>으로 늘어날 것이다...!

특히, 가장 computer 자원을 많이 소모하는 reflow와 repaint를 변화마다 일일히 해버리게 될 것이다.

```javascript
// Real DOM
const cost = (reflow + repaint) * n
```

하지만 **Virtual DOM은 그 변화가 많아져도 그 성능을 일정하게 유지해준다!**

## 원리

만약 10개 정도의 변화가 발생했다면, 10번 Real DOM 갱신을 하는 것이 아닌 변화들을 Virtual DOM에 모두 적용시키고 단 1번! Real DOM을 갱신하는 원리이다.

**즉, 바뀔때마다 Real Dom을 새로 그리는게 아닌, 일정 시간 혹은 횟수의 변화들을 모아서 단 1번만 re-render할 수 있도록 Optimization 해주는 것이다.**

```javascript
// React
const cost = (VDOM refresh) * n + (reflow + repaint)

여기서 cost는 (VDOM refresh) <<<<<<<<<< (reflow + repaint)
```

(이것이 setState가 비동기인 이유이기도 하다!)

(컴퓨터 자원의 소모는 [여러번의 reflow > 한번의 큰 reflow] 이다)

virtual DOM은 render할 필요가 없기 때문에 **reflow, repaint 과정이 필요없어** 훨씬 빠르게 갱신이 되고, 컴퓨터 자원도 훨씬 덜 소모한다.

일정 시간동안 모인 변화들은 이제 Real Dom을 갱신할 준비가 되었다...!

이제 Real DOM과 diffing하여 바꿀 곳을 확인하고 한번에 적용한다.

# batching 자동화

![AutomaticWrapping]({{ "/assets/img/gifs/automaticWrapping.gif" | relative_url }})

사실 vanilla javascript로도 DOM 변경 사항들의 batching이 충분히 가능하다.

(여기서 batching이란 변경 사항을 바로 적용하지 않고 모아두는 것을 말한다.)

그렇다면 react를 쓸 이유가 없는데...?

하지만 어떤 interaction을 넣을때 마다, 일어날 변화들을 전부 수동으로 일일히 batching해주고... 변경사항이 있을 때 마다 수정하고 한다면...?

굉장히 귀찮을 것이다.

**React는 그것을 알아서 해준다.**

알아서 적당히 최적화해주는 것이다.

기특한 것...!

# Browser의 발전과 React

최근 Browser들은 굉장히 똑똑해져서 react처럼 여러 변경 사항들을 모아서 한번에 render시켜주는 기능이 있다.

하지만 그럼에도 React를 사용하는 이유는

**component의 재사용성**과 **깔끔한 코드 관리**

**jsx와 Data flow의 직관성** 때문이 아닌가 싶다.

# 앞으로의 React

![spongebobStudyHard]({{ "/assets/img/gifs/spongebobStudyHard.gif" | relative_url }})

현재 React를 잘 사용하고 있고 성능과 코드 관리 면에서 크게 만족하고 있지만,

Browser들의 발전이나 Svelte의 출현을 보면

현재 익숙한 기술에 안주할 것이 아니라 계속 더 좋은 것을 찾아

연구하고 피드백하는 자세가 필요하다고 생각한다.

# ref

[](https://stackoverflow.com/questions/61245695/how-exactly-is-reacts-virtual-dom-faster)

[](https://svelte.dev/blog/virtual-dom-is-pure-overhead)

[](https://dev.to/karthikraja34/what-is-virtual-dom-and-why-is-it-faster-14p9)
