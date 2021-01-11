---
layout: post
title: Module
tags: [JavaScript, AJAX, Theories]
excerpt_separator: <!--more-->
---

# Module

Reusable piece of code that *encapsulates* implementation details

Usually a *standalone file*, but it doesn't have to be.

<!--more-->

## public API?

Module에서 Export 되는 것을 전부 public API라고 부른다.

# Module식 개발

![ModernJavascriptDevelopment]({{ "/assets/img/aboutJavaScript/ModernJacascriptDevelopment.PNG" | relative_url }})

# 과거 module

IIFE 형태로 function을 만들어 module로 활용했던 것과 함께

외부 source를 활용하여 module을 실현한 것이 있다.

- AMD module
- CommonJS module

이 중 CommonJS module은 아직 그 가치가 유효하다. (parcel에서도 지원한다.)

```javascript
//commonJS module
// npm과 함께 개발된 것으로 오로지 Node를 겨낭해서 만들어서 지금도 유효하고 현재 코드들도 commonJS에 사용가능하다
// 따라서 현재 code는 browser에선 작동안하지만 node에선 작동한다.
// 특징 : 1파일에 1모듈이다.
export.addToCart = function(product, quantity){
    cart.push({product, quantity});
    console.log(`${quantity} ${product} added to cart`)
};

const { addToCart } = require('./shoppingCart.js');
```