---
layout: post
title: Type & Deal with Korean in Browser
tags: [Korean, IME, Input, composition event]
excerpt_separator: <!--more-->
---

# Korean, composition word

라틴어 기반의 문자들은 알파벳을 단순 나열하여 단어를 만들고 문장을 만들 수 있습니다.

게다가 사람 손이 닿는 키보드 안에 모든 알파벳을 담을 수 있습니다.

하지만, 한국어는 다릅니다. 조합가능한 모든 한국어를 키보드로 준비할 수 없어, 자음과 모음으로 자판이 나뉘어졌고, 자음과 모음을 타이핑해 조합해서 하나의 글자를 만들며 단어와 문장을 만듭니다.

독특한 한국어가 컴퓨터에서 다뤄지는 방법, 브라우저에서 다뤄지는 방법에 대해 알아보겠습니다.

![korean_typing_thumbnail]({{ "../assets/img/aboutKoreanInput/korean_typing.png" | relative_url }})

<!--more-->

# 겹자음 이슈

현재 근무하는 에듀테크 회사의 수학 문제 중에 ㄱ,ㄴ,ㄷ,ㄹ 형식으로 보기가 주어지고, 주관식 input에 보기를 자유롭게 입력하는 방식을 사용하는 문제가 있습니다.

하지만, 겹자음 이슈로 "ㄹ,ㄱ을 의도하고 타이핑하면 "ㄹㄱ"으로 입력되고, 이대로 제출하면 틀린 답을 제출했다고 하여 학생들로부터 직접 이슈를 받았습니다. (Window 환경에서 이슈를 많이 받았습니다.)

이를 해결하기 위해 한국어 입력 방법에 대해 이해하고 해결 방법을 생각해보고자 합니다.

# 인코딩과 한국어

우선 컴퓨터가 문자를 이해하고 다루는 방식을 알아야겠습니다. 먼저 인코딩 대해 알아보겠습니다.

```html
  <meta charset="UTF-8">
```

HTML을 다뤄보신 분들은 위 태그를 본 적이 있을 것입니다.

위 태그는 해당 HTML 문서의 문자 인코딩 방식을 UTF-8로 설정하는 태그입니다.

UTF-8은 이 세상 대부분의 문자와 심볼을 표현할 수 있는 문자 인코딩 방식으로 글로벌 환경에 서빙되는 web 특성상 가장 권장되는 방식입니다.

## Encoding

인코딩(encoding)은 어떤 정보를 컴퓨터가 이해할 수 있는 형식으로 변환하는 과정을 말합니다.

문자의 경우 문자를 컴퓨터가 이해할 수 있는 0과 1의 조합으로 변환하는 과정을 말합니다. (비디오나 오디오 데이터도 인코딩이 필요합니다.)

인코딩엔 여러 종류가 있으며, 각각 특정 문자 집합을 컴퓨터가 이해할 수 있는 코드로 변환하는 규칙을 정의합니다.

- UTF-8 : 국제 표준으로 가변 길이 인코딩 방식으로 영어 및 숫자나 기호는 ASCII, 이외 문자는 2바이트나 3바이트로 나타냅니다. 한국어는 3바이트입니다. 주로 리눅스, HTML 같은 글로벌 환경에서 사용됩니다.
- UTF-16 : 2바이트 또는 4바이트로 문자를 표현합니다. BMP(기본 다국어 평면)의 문자는 2바이트로, 이외의 문자는 4바이트로 표현합니다. 주로 윈도우, 자바 환경에서 사용됩니다.
- ASCII : 최초의 문자셋으로, 7비트 구성(0 ~ 127)으로 알파벳, 숫자, 특수 문자 등 간단한 텍스트를 다룰 때 사용됩니다.
- Euc-kr : euc는 extend unix code의 약자로 유닉스에서 영어를 제외한 문자를 표시하기 위한 확장 부호를 의미합니다.

# IME (Input Method Editor)

지금까지 문자가 컴퓨터에서 다뤄지는 방식에 대해 알아봤습니다. 그럼 이제부턴 한국어가 자판 입력으로 한국어가 완성되는 방식에 대해 알아보겠습니다.

IME란, 한글, 한자처럼 컴퓨터 자판에 있는 글쇠보다 수가 더 많은 문자를 계산하거나 조합하여 입력해 주는 OS 단계의 시스템 소프트웨어입니다.

보통 단위 문자는 그 자체로 소리나 뜻을 품고 있습니다.

한국어의 경우, "ㄱㄴㄷㄹ", "ㅏㅣㅗ"와 같은 자음과 모음은 그 어떤 소리나 뜻을 품고 있지 않기 때문에 자음과 모음을 조합하여 글자를 만들어야 합니다.

이 때, 자음과 모음으로 이루어진 글쇠 자판으로 컴퓨터 환경에서 자음과 모음을 합해 글자를 만들기 위해선 IME가 필요합니다.

(이는 표음 문자로 한자를 만들어내는 중국어, 일본어도 동일합니다.)

# 한국어 입력기 오토마타

IME는 많은 사람들이 그 원리를 이해하기 위해 간단한 프로젝트로서 오토마타를 만들어보곤 합니다.

한국어 또한, 자음과 모음을 조합하여 글자를 만들어내는 방식을 오토마타로 표현할 수 있습니다.

아래는 한국어 오토마타를 만드신 분의 다이어그램입니다. 대략 이런식으로 동작한다고 이해하시면 됩니다.

![korean-automata]({{ "../assets/img/aboutKoreanInput/korean_automata.webp" | relative_url }})

# 브라우저의 한국어

브라우저에서 한국어 입력을 다루는 것을 알아보겠습니다.

## isComposition

isComposition은 `true`일 때, 한국어 입력 중임을 나타냅니다.

compositionStart와 compositionEnd 이벤트 사이에 해당 이벤트가 발생합니다.

React에선 KeyboardEvent의 `event.nativeEvent.isComposing`을 통해 한국어 입력 중임을 알 수 있습니다.

## composition event

composition event는 한국어 입력 중에 발생하는 이벤트입니다.

- compositionstart : 한국어 조합이 시작될 때 발생합니다.
- compositionupdate : 한국어 조합 중에 발생하는 이벤트입니다.
- compositionend : 한국어 조합이 끝날 때 발생합니다.

보통 한국어는 "ㄱ"만 입력해도 글자의 조합이 시작되기 때문에 composition 이벤트는 상시 일어난다고 할 수 있습니다.

# 브라우저에서 한국어 다뤄보기 - 겹자음이 입력 안되도록 해보기

현재 근무하는 에듀테크 회사의 수학 문제 중에 ㄱ,ㄴ,ㄷ,ㄹ 형식으로 보기가 주어지고, 주관식 input에 보기를 자유롭게 입력하는 방식을 사용하는 문제가 있습니다.

하지만, 겹자음 이슈로 "ㄹ,ㄱ을 의도하고 타이핑하면 "ㄺ"으로 입력되고, 이대로 제출하면 틀린 답을 제출했다고 하여 학생들로부터 직접 이슈를 받았습니다.

input에서부터 겹자음을 분리시켜 입력되도록 하기 위해 아래와 같이 state로 input의 value를 조정하는 controlled component 방식으로 input을 제어해도, 특정 한국어 composition은 막을 수 없었습니다.

아래와 같은 코드로 테스트해보았습니다.

```jsx
const DISASSEMBLED_CONSONANTS_BY_CONSONANT = {
  ㄳ: 'ㄱㅅ',
  ㄵ: 'ㄴㅈ',
  ㄶ: 'ㄴㅎ',
  ㄺ: 'ㄹㄱ',
  ㄻ: 'ㄹㅁ',
  ㄼ: 'ㄹㅂ',
  ㄽ: 'ㄹㅅ',
  ㄾ: 'ㄹㅌ',
  ㄿ: 'ㄹㅍ',
  ㅀ: 'ㄹㅎ',
  ㅄ: 'ㅂㅅ',
} as const;

function App() {
  const [input, setInput] = useState('');
  console.log('input',input)

  return (
    <>
      <div>
        <a href="https://vite.dev" target="_blank">
          <img src={viteLogo} className="logo" alt="Vite logo" />
        </a>
        <a href="https://react.dev" target="_blank">
          <img src={reactLogo} className="logo react" alt="React logo" />
        </a>
      </div>
      <h1>Vite + React</h1>
      <div className="card">
        <input
          value={input}
          onCompositionStart={(e) => {
            console.log('onCompositionStart');
            e.preventDefault();
          }}
          onCompositionEnd={(e) => {
            console.log('onCompositionEnd');
            e.preventDefault();
          }}
          onChange={(e) => {
            const value = e.target.value;
            console.log('onChange', value)
            const lastword = value[value.length - 1];
            const 겹자음푼문자 = DISASSEMBLED_CONSONANTS_BY_CONSONANT[lastword];

            if (겹자음푼문자) {
              setInput(겹자음푼문자);
              return;
            }
            setInput(value);
          }}
        />
        <p>
          Edit <code>src/App.tsx</code> and save to test HMR
        </p>
      </div>
      <p className="read-the-docs">
        Click on the Vite and React logos to learn more
      </p>
    </>
  )
}
```

다음은 데모영상입니다.

![korean_input_demo]({{ "../assets/img/aboutKoreanInput/korean_input_demo.gif" | relative_url }})

겹자음이 완성되고 composition event가 끝나면 그 다음 자판 입력은 원래 합성된 문자가 있어야 할 그 다음 자리에 입력되어 덮어씌워지기 때문에 이를 막을 수 없었습니다.

원래와 다르게 onCompositionEnd 이벤트가 건너띄워지는데, 왜 그런지는 좀 더 공부해야봐야할 것 같습니다.

# 정리

독특한 한국어 이슈를 가지고 한국어 입력에 대한 내용을 다뤄봤습니다.

여러가지를 공부하면서 흥미로웠고, 특히 한국어 오토마타가 꽤나 흥미로웠습니다!

언제 한번 시간이 된다면 상태 머신을 공부하면서 그 예시 프로젝트로 한번 만들어보고 싶네요.

감사합니다.

# ref.

- [입력기 - 위키백과](https://ko.wikipedia.org/wiki/%EC%9E%85%EB%A0%A5%EA%B8%B0)
- [KeyboardEvent.isComposing - MDN](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent/isComposing)
- [compositionstart Event - MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/compositionstart_event)
- [compositionupdate Event - MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/compositionupdate_event)
- [compositionend Event - MDN](https://developer.mozilla.org/en-US/docs/Web/API/Element/compositionend_event)
- [네이버 D2 - 한글 입력과 관련된 이야기](https://d2.naver.com/helloworld/19187)
- [Velog - 한글 인코딩](https://velog.io/@dondonee/%ED%95%9C%EA%B8%80-%EC%9D%B8%EC%BD%94%EB%94%A9)
- [나무위키 - 입력 방식 편집기](https://namu.wiki/w/%EC%9E%85%EB%A0%A5%20%EB%B0%A9%EC%8B%9D%20%ED%8E%B8%EC%A7%91%EA%B8%B0)
- [네이버 널리 - 한글 입력과 관련된 개발 경험](https://nuli.navercorp.com/community/article/1079940)
- [나무위키 - 오토마타](https://namu.wiki/w/%EC%98%A4%ED%86%A0%EB%A7%88%ED%83%80)
- [Medium - 한글 입력 오토마타 이야기](https://medium.com/happyprogrammer-in-jeju/%ED%95%9C-%EB%8B%AC%EC%A7%9C%EB%A6%AC-%EA%B0%9C%EC%9D%B8-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%9D%B4%EC%95%BC%EA%B8%B0-3-%ED%95%9C%EA%B8%80-%EC%9E%85%EB%A0%A5-%EC%98%A4%ED%86%A0%EB%A7%88%ED%83%80-60e833dd1f60)
- [티스토리 - IME와 한글 입력](https://ph-biginner.tistory.com/135)
- [React - CompositionEvent Handler](https://react.dev/reference/react-dom/components/common#compositionevent-handler)
