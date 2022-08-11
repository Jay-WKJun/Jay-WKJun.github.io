---
layout: post
title: react-web-code-editor 개발기
tags:
  [
    TypeScript,
    webpack,
    rollup,
    esm,
    cjs,
    tree shaking,
    library,
    npm,
    npm package,
  ]
excerpt_separator: <!--more-->
---

npm으로 간단하게 설치해서 사용할 수 있는 react-web-code-editor 개발기입니다!

**2022년 1월 ~ 3월 v.1 개발** 그리고 **4월 ~ 5월 v.2 개발**로 좀 늦은 후기이지만, 올려보겠습니다! 🙌

![npm_README]({{ "../assets/img/reactCodeEditor/npm_readme.png" | relative_url }})

<!--more-->

# 🧐 개발 동기

코드 에디터를 만들어보자! 라는 개발 동기는 제 첫 프로젝트와 관련있습니다.

바닐라 코딩 부트캠프 당시에 "GitCord"라는 실시간 개발자 협업 애플리케이션을 개발했던 경험이 있습니다.

3주라는 짧은 기간때문에, 코드 에디터를 "Code mirror"라는 라이브러리를 사용해 완성했었는데, 원래는 코드 에디터까지 직접 개발하는 것이 계획이었어서 아쉬움이 많이 남았습니다.

따라서, 이번 기회에 모두가 사용할 수 있는 라이브러리 형태의 코드 에디터를 만들기로 결정했습니다. 🙌

## 그 외, 개발 목표!

그 외에도 코드 에디터 라이브러리 개발을 통해 얻고자 하는 것들이 있었습니다.

(역시 사이드 프로젝트는 재미와 함께 긍정적인 목표들도 함꼐 이룰 수 있는 것 같아 좋은 것 같습니다! 😆)

- 오픈 생태계를 좀 더 경험하고 싶었다.
- 스스로 문제를 정의하고, 기술적으로 문제를 풀어내는 경험을 해보고 싶었다!
- 라이브러리 배포와 관련 기술들을 경험해보고 싶었다.
- 누구나 사용할 수 있도록 적절히 추상화해보는 경험과 다른 사람들의 로직에 간섭하지 않도록 데이터와 로직을 확실하게 캡슐화해보는 경험을 해보고 싶었다.

# 🎯 개발 목표!

개발 전 어떤 사람들이 사용할 것인지, 어떤 기능을 구현할 것인지에 대해 간단히 정리했습니다!

## 👨‍👩‍👧‍👦 예상한 사용자들

react-code-editor 라이브러리는 Visual Studio Code같은 현업에서도 쓰는 Super Editor는 아니지만, **어디서든 사용할 수 있는 메모장 같은 느낌의 코드에디터**를 목표로 개발했습니다.

메모장이나 포스트 잇처럼 사용해주셨으면 하는 마음이었습니다. 😊

(의도대로라면 chrome extension으로 확장도 가능하지 않을까?! 라는 생각도 있습니다.)

## ⚙️ 차별화된 특징

여러 code editor가 있지만 그 중에서도 가장 특별하고 유용한 기능을 생각했습니다! 🤩

- custom theme, code syntax styling 기능

사용자가 code-editor의 전체적인 테마와 code syntax들의 style을 적용할 수 있도록 지원하고 있습니다!

아래는 customTheme을 적용한 데모입니다.

![customThemeDemo]({{ "../assets/img/reactCodeEditor/custom_theme_demo.png" | relative_url }})

```tsx
import React from "react";
import "./App.css";
import CodeEditor, { addTheme } from "react-web-code-editor";

/* this custom theme name is "customeTheme" */
addTheme("customTheme", {
  backgroundColor: "Black",
  caretColor: "White",
  textColor: "LightGreen",
  keywords: {
    boolean: "Pink",
    number: "Yellow",
    string: "Green",
    comment: "Violet",
    function: "White",
    keyword: "Blue",
    ["class-name"]: "LightCoral",
    property: "MediumSeaGreen",
    operator: "Cyan",
    punctuation: "Red",

    /* in Example, you can see upper style from this line has been applied */

    prolog: "AliceBlue",
    doctype: "Beige",
    variable: "Wheat",
    cdata: "Brown",
    tag: "Olive",
    constant: "DeepPink",
    symbol: "Indigo",
    deleted: "Blue",
    selector: "DarkGreen",
    ["attr-name"]: "ForestGreen",
    char: "Ivory",
    builtin: "Linen",
    inserted: "MediumSpringGreen",
    entity: "BurlyWood",
    url: "RoyalBlue",
    ["language-css"]: "AliceBlue",
    style: "Chocolate",
    /* you can use "Hex color code" */
    atrule: "#a74a4a",
    ["attr-value"]: "#000000",
    /* you can use "rgb" or "rgba" code */
    regex: "rgb(0,0,0)",
    important: "rgba(167,74,74,0.6)",
  },
});

function App() {
  return (
    <div className="App">
      <header className="App-header">
        {/* now, you can use "customTheme" */}
        <CodeEditor theme="customTheme" width="210px" height="210px" />
      </header>
    </div>
  );
}

export default App;
```

그 외의 기능들은 라이브러리 소개는 github repo의 [README](https://github.com/Jay-WKJun/react-web-code-editor)에서 자세히 소개하고 있습니다! 👈

# 📚 개발 Log

여기선 개발하면서 있었던 여러가지 이야기를 해보려고 합니다.

## pre 태그

> HTML <pre> 요소는 미리 서식을 지정한 텍스트를 나타내며, HTML에 작성한 내용 그대로 표현합니다. 텍스트는 보통 고정폭 글꼴을 사용해 렌더링하고, 요소 내 공백문자를 그대로 유지합니다. - MDN

textArea를 통해 입력한 글자를 그대로 표현하기 위해 pre 태그를 활용했습니다.

### div와 pre의 차이

div  | pre
------------- | -------------
![div](../assets/img/reactCodeEditor/code_with_div.png)  | ![pre](../assets/img/reactCodeEditor/code_with_pre.png)

TODO: (webpack -> rollup 적용기)
## rollup.js 적용기

react-web-code-editor v.1의 번들러는 webpack 🗳이었습니다.

하지만 v.2에서 rollup 🗞 으로 번들러를 교체하였는데요!

그 이유와 rollup 적용기에 대해 말씀드리겠습니다! 🙌

### ES Module과 Tree Shaking

사용자는 제 라이브러리를 최대한 가볍게 사용하길 원합니다.

따라서 사용자가 사용한 기능만 제공해주는 것이 가장 바람직합니다.

여기서 **Tree Shaking**이라는 개념이 나옵니다.

![treeShaking](../assets/img/reactCodeEditor/tree_shaking.png)

Tree Shaking이란 나무를 흔들어 필요없는 낙엽을 떨어뜨리는 것처럼,

사용되지 않는 코드를 번들에 포함시키지 않도록 하여 최대한 번들을 가볍게 하는 것을 말합니다.

이 Tree Shaking은 ES Module을 통해 가능합니다.

**ES Module**

(cjs와 esm의 설명, 왜 esm으로 할려고 했는지)

(esm의 static module structure와 cjs의 dynamic을 설명하고 뭐가 다른지 설명)

https://exploringjs.com/es6/ch_modules.html#static-module-structure
(+ what the static means!?)
(위 설명을 통해 esm만 tree shaking이 가능한 이유 설명)
(cjs는 왜 모든것을 불러와야 하는지 설명)
(다른 사람들의 코드 tree shaking 결과를 좀 가져다 쓰자.)

(webpack과 rollup의 차이를 들어 rollup이 esm을 지원해준다는 사실을 명시)

(직접 적용해보니 이랬다는 후기)

# 후기
