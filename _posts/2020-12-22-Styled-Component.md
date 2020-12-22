---
layout: post
title: Styling Methods in react
tags: [react, javascript, css, styledComponent, ]
excerpt_separator: <!--more-->
---

# Styling Methods

react에는 여러가지 styling method가 있다.

1. Radium
2. Styled component
3. CSS module

<!--more-->

# Radium

일반적으로 변수에 css 속성을 설정하고 JSX의 Style attribute에 설정하면 되지만

media query나 pseudo class등의 동적인 css는 적용되지 않는다.

이를 해결하기 위한 third party app이 "Radium"이다.

```javascript
import Radium, {StyleRoot} from "radium"

class App extends Component {
  state = {
    persons: [
      {id: "1", name: ""}
    ],
    otherValue: "for the Test"
  }

  changePersonNameHandler = (event) => {
    let persons = [...this.state.persons]
    let input = event.target.value;
    let id = event.target.id;
    let targetPersonIndex = persons.findIndex(el => el.id === id);
    if(targetPersonIndex !== -1){
      persons[targetPersonIndex].name = input;
    }else{
      const randomId = Math.floor(Math.random() * 1000);
      const newOb = {
        id: randomId,
        name: input
      }
      persons.push(newOb);
    }
    this.setState({
      persons: persons
    });
  }

  render() {
    const style = {
        backgroundColor:'green',
        color: "white",
        font: "inherit",
        border: "1px solid blue",
        padding: "8px",
        cursor: "pointer",
        //원래는 text-align이지만 JSX의 법칙에 따라 camel case로 작성
        textAlign: "center",
        //1. 여기서 직접 적용해도 되고
        ":hover": {
            backgroundColor: "lightgreen",
            color: "black"
        }
    }

    //2. 밖에서 수정해 적용하는 것도 가능하다.
    style.background = "red";
    style[":hover"]: {
        backgroundColor: "lightgreen",
        color: "black"
    }

    //mediaQuery
    const mediaStyle = {
        "@media (min-width: 500px)": {
            width: "450px"
        }
    };

    return (
        //!!! meidaQuery 같은 동적인 css를 적용하기 위해선 <StyleRoot>로 반드시 감싸줘야한다.
        <StyleRoot>
            <div className="App">
                {
                this.state.persons.map((person, index) => {
                    return (<div>
                    <PersonInput key={person.id} id={person.id} change={this.changePersonNameHandler} style={mediaStyle}></PersonInput>
                    </div>);
                })
                }
                <button style={style} onClick={this.plusPersonDivHandler}>Plus more person div</button>
            </div>
        </StyleRoot>
    );
  }
}

//Radium 사용법 : export시에 이렇게 Radium으로 감싸줘야한다.(style을 먹이고 싶은 component 모두에 이렇게 하면 된다.)
export default Radium(App);
```

# Styled Component

React에서 가장 널리쓰이는 것으로 css문법을 그대로 사용할 수 있고 적용한 것이 head tag에 적용되기 때문에 적용을 원하는 component나 tag에 똑같이 중복적용할 수 있다.

JS 내부에서 스타일을 정의한다.

```javascript
import styled from "styled-components";

class App extends Component {

    width = "60%";

    //div tag안에 적용될 css를 css문법 그대로 적어 넣는다.
    //sass문법도 적용된다.
    //JS의 string 전략도 적용 가능핟.
    StyledDiv = styled.div`
            width: ${this.width},
            margin: 16px auto;
            border: 1px solid black;
            box-shadow: 0 2px 3px black;
            padding: 16px;
            text-align: center;

            &: hover: {
                background-color: red;
                color: black;
            }

            @media(min-width: 500px) {
                width: 450px;
            }
            `

  render() {
    return (
        //위에 css를 적용한 div를 이곳에 그대로 적용한다는 뜻이다.
        <StyledDiv>
            <p>styled component</p>
        </StyledDiv>
    );
  }
}

export default App;
```

# CSS Module

CSS 클래스를 만들면 자동으로 고유적인 클래스네임을 만들어서 스코프를 지역적으로 제한하는 방식

즉, 특정 component에만 .css를 적용 시키고 싶을 때 사용한다.(css를 import하면 같은 page안에 같은 class 이름이 있을 경우 해당 css를 모두에게 적용시킨다.)

특이하게 이건 install이 필요없이 webpack configure만 바꾸어 사용이 가능하다. 구체적으로 웹팩의 css-loader 를 통하여 불러오는 방식

## Web-pack configuration

!! react-script v.2 이상이라면 person.module.css로 css파일을 만들고 import하여 그대로 사용하면 eject하여 configure해줄 필요가 없이 자동으로 CSS Module을 적용시켜준다.

---

사용하기에 앞서 설정을 해주어야한다.

우선 **<u>npm run eject</u>** 으로 web-pack config를 보이게 하고 **<u>config/webpack.config.dev.js</u>**를 열어 css-loader를 찾는다.

보통은 이렇게 되어있다.

```javascript
{
        test: /\.css$/,
        use: [
          require.resolve('style-loader'),
          {
            loader: require.resolve('css-loader'),
            options: {
                //이곳이 수정할 곳!
              importLoaders: 1,
            },
          },
          {
            loader: require.resolve('postcss-loader'),
            options: {
              // Necessary for external CSS imports to work
              // https://github.com/facebookincubator/create-react-app/issues/2677
              ident: 'postcss',
              plugins: () => [
                require('postcss-flexbugs-fixes'),
                autoprefixer({
                  browsers: [
                    '>1%',
                    'last 4 versions',
                    'Firefox ESR',
                    'not ie < 9', // React doesn't support IE8 anyway
                  ],
                  flexbox: 'no-2009',
                }),
              ],
            },
          },
        ],
      },
```

위의 3가지 loder들은

1. style-loader 가 스타일들을 불러와서 페이지에서 활성화 해주는 역할
2. css-loader 는 css 파일에서 import 와 url(...) 문들을 webpack 의 require 기능을 통하여 처리해주는 역할
3. postcss-loader 의 경우에는, 우리가 입력한 CSS 구문이 모든 브라우저에서 제대로 작동할 수 있도록 자동으로 -webkit, -mos, -ms 등의 접두사를 붙여준다.

이 중 css-loader에 집중! option에서 CSS Module을 사용하도록 아래 두가지를 option에 설정해주면 된다.

```javascript
    modules: true,
    localIdentName: '[path][name]__[local]--[hash:base64:5]'

    //필수적인것을 포함했다면 형식은 마음대로 설정할 수 있다!
    //localIdentName: '[name]__[local]__[hash:base64:5]'
```

첫번째 속성은 CSS Module 을 활성화, 두번째 속성 localIdentName 은 CSS Module 에서 고유적으로 생성되는 클래스네임의 형식을 정해준다. 해당 형식에 따라 random하게 이름이 붙는다.

webpack.config.dev.js와 마친가지로 webpack.config.prod.js에도 동일하게 적용!

webpack.config.dev.js 는 우리가 개발 할 때 사용하는 웹팩 개발 서버 전용 설정이며, webpack.config.prod.js 는 나중에 리액트 프로젝트를 완성하고 배포하게 될 때 빌드하는 과정에서 사용되는 환경설정 파일이다.

이제 적용해보면!

```css
.box {
    display: inline-block;
    color: white;
    font: inherit;
    border: 1px solid blue;
    padding: 8px;
    cursor: pointer
}
.inner {
    background-color: green;
}
```

```javascript
import style from './App.css'

class App extends Component {

  render() {
      //이렇게 class를 합쳐 여러 css를 한번에 적용시킬 수도 있다.
    const assignedClass = [style.box, style.inner].join(" ");

    return (
        //broser에서 ClassName을 보면 random으로 생성된걸 확인할 수 있다.
        <div>
            <p className={style.box}>styled component</p>
            <div className={assignedClass}></div>
        </div>
    );
  }
}

export default App;

```

ref: https://velopert.com/3447