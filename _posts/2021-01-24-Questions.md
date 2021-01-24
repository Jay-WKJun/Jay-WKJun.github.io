---
layout: post
title: Question stack overflow!
tags: [question, questions]
excerpt_separator: <!--more-->
---

# Question stack overflow!

궁금한거 모음집...

## parameter reassign problem

parameter reassign이 왜 optimization에 문제를...?

7.13 Never reassign parameters. eslint: no-param-reassign

Why? Reassigning parameters can lead to unexpected behavior, especially when accessing the arguments object. <u><b>It can also cause optimization issues, especially in V8.</b></u>

// bad
function f1(a) {
  a = 1;
  // ...
}

function f2(a) {
  if (!a) { a = 1; }
  // ...
}

// good
function f3(a) {
  const b = a || 1;
  // ...
}

function f4(a = 1) {
  // ...
}

## Do not include JavaScript filename extensions

10.10 Do not include JavaScript filename extensions eslint: import/extensions

Why? Including extensions inhibits refactoring, and inappropriately hardcodes implementation details of the module you're importing in every consumer.

// bad
import foo from './foo.js';
import bar from './bar.jsx';
import baz from './baz/index.jsx';

// good
import foo from './foo';
import bar from './bar';
import baz from './baz';