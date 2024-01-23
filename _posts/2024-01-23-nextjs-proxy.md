---
layout: post
title: Proxy in Next.js
tags: [Frontend, nextjs, proxy, API, CORS, rewrite, redirect, forwarding]
excerpt_separator: <!--more-->
---

# Next.js의 proxy 기능

Next.js는 기본적으로 웹 프론트도 가능하지만, 서버까지 함께있는 풀스택 프레임워크 입니다.

여기서 Next.js가 제공하는 서버의 여러가지 proxy 기능을 활용하여 다양한 것을 할 수 있습니다.

![reverseProxy]({{ "../assets/img/aboutInternet/reverseProxy.webp" | relative_url }})

<!--more-->

# Proxy란?

Proxy는 클라이언트와 서버 사이에 위치하여, 클라이언트의 요청을 받아서 서버에 전달하고, 서버의 응답을 받아서 클라이언트에 전달하는 중계자 서버 역할을 합니다.

Proxy 서버는 그 위치에 따라 2가지로 나뉩니다.

## Forward vs Reverse Proxy

Forward는 클라이언트 바로 뒤에 위치합니다.

반면 Reverse Proxy는 서버 바로 앞에 위치합니다. (여기서 Reverse는 "반대"라는 의미가 아닌, "배후, 뒤쪽"라는 의미입니다.)

**Next.js가 제공하는 Proxy는 서버 바로 앞에 위치하기 때문에 Reverse Proxy입니다.**

![forwardVsReverse]({{ "../assets/img/aboutInternet/forwardVsReverse.jpeg" | relative_url }})

# redirect vs rewrite

Next.js에서 제공하는 proxy 기능은 **redirect**와 **rewrite**가 있습니다.

redirect는 말 그대로 redirect, rewrite는 forwarding과 대응한다고 할 수 있겠네요!

## redirect vs forwarding

redirect와 forwarding은 백엔드에서 자주 사용되는 용어입니다.

![redirectVsForwarding]({{ "../assets/img/aboutInternet/redirectVsForwarding.png" | relative_url }})

간단히 이렇게 설명할 수 있습니다.

- Redirect : 요청 -> 다른 곳으로 요청하라고 응답 (code: 3xx) -> **클라이언트에서** 다시 요청 -> 응답
- Forwarding : 요청 -> **서버내에서** 다른 곳에 요청하고 응답 받아옴 -> 그대로 결과 응답

## Example

적용 예시를 보시겠습니다. 😎

### redirect

redirect를 적용한 코드와 페이지 스샷입니다.

![redirectCode]({{ "../assets/img/nextjs/proxy/redirectCode.png" | relative_url }})

![redirectEx]({{ "../assets/img/nextjs/proxy/redirectEx.png" | relative_url }})

여기서! permanent 옵션에 따라 307, 308로 응답코드가 나눠집니다.

이는, 웹표준을 준수한 것으로 자세한 내용은 [MDN, 300 Multiple Choices](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/300)를 참고해주세요.

### rewrite

rewrite를 적용한 코드와 페이지 스샷입니다.

![rewriteCode]({{ "../assets/img/nextjs/proxy/rewriteCode.png" | relative_url }})

![rewriteEx]({{ "../assets/img/nextjs/proxy/rewriteEx.png" | relative_url }})

### dynamic path & 외부 url

고정 url뿐만 아니라, dynamic path와 외부 url도 적용할 수 있습니다.

![redirectDynamicCode]({{ "../assets/img/nextjs/proxy/redirectDynamicCode.png" | relative_url }})

![redirectDynamic]({{ "../assets/img/nextjs/proxy/redirectDynamic.gif" | relative_url }})

## Other options

위에서 보셨듯이 source로 proxy할 요청 url을 지정했는데요.

추가 조건을 주어 proxy 대상을 더 세세히 판단할 수 있습니다.

`has`로 must have를 `missing`으로 must not have를 지정할 수 있습니다.

v.14.1 기준 4가지 타입을 적용할 수 있습니다.

- header
- cookie
- query
- host

자세한 건 -> [Next.js, rewrite header-cookie-and-query-matching](https://nextjs.org/docs/pages/api-reference/next-config-js/rewrites#header-cookie-and-query-matching)

# beforeFiles, afterFiles, fallback

**rewrite에만 있는 옵션**으로, 좀 더 세세하게 rewrite를 적용할 수 있습니다.

## Next.js의 요청 및 응답 순서

Next.js에 어떤 요청이 오면, 다음과 같은 순서로 처리됩니다.

1. Header check/response
2. redirect check/response
3. **beforeFiles** check/response
4. static files check/response
5. **afterFiles** check/response
6. Dynamic route page check/response
7. **fallback** check/response

이 순서를 잘 파악해서 사용하면 좋을 것 같습니다.

### beforeFiles

정적 파일들이 등록된 api를 확인하기 전에 proxy가 적용됩니다.

아래와 같이 설정했다고 가정해보겠습니다.

```javascript
rewrites: async ()  => ({
  beforeFiles: [{
    source: '/test/:path*',
    destination: '/proxy/:path*',
  }],
})
```

`/test`는 정적 컴포넌트가 있는 경로입니다.

여기서 **`${url}/test`에 요청하면 `/test`에 등록된 정적 컴포넌트가 아닌 `/proxy`로 요청이 proxy됩니다.**

### afterFiles

정적 파일들이 등록된 api를 확인한 후에 proxy가 적용됩니다.

```javascript
rewrites: async ()  => ({
  afterFiles: [{
    source: '/test/:path*',
    destination: '/proxy/:path*',
  }],
})
```

동일하게 `/test`는 정적 컴포넌트가 있는 경로고,`${url}/test`에 요청하면, `/proxy`로 요청이 proxy되지 않고, `/test`에 등록된 정적 컴포넌트가 응답됩니다.

여기서 **`/test/[id]`와 같이 동적 경로가 등록되어 있다면, `/test/1`에 대한 요청은 `/proxy/1`로 proxy됩니다.**

### fallback

fallback은 정적 파일과 동적 경로가 등록된 api를 확인한 후에 proxy가 적용됩니다.

```javascript
rewrites: async ()  => ({
  fallback: [{
    source: '/test/:path*',
    destination: '/proxy/:path*',
  }],
})
```

위와 동일한 가정하에, `{url}/test`, `{url}/test/[id]`로 요청하면, proxy되지 않습니다.

오로지, **아무것도 등록되지 않은 api에 접근했을 떄만, fallback에 등록한 proxy가 적용됩니다.**

# rewrite 활용

Next.js 공식 홈페이지에서 여러가지 활용 방안을 소개하고 있습니다.

- CORS 해결
- 점진적인 페이지 마이그레이션
- [Multi zone](https://nextjs.org/docs/pages/building-your-application/deploying/multi-zones)이라는 이름으로 Micro frontend를 지원하네요.
  이것 또한 rewrite를 응용한 것 입니다.

# Ref

- https://nextjs.org/docs/app/api-reference/next-config-js/redirects
- https://nextjs.org/docs/pages/api-reference/next-config-js/rewrites

- https://www.cloudflare.com/ko-kr/learning/cdn/glossary/reverse-proxy/
- https://inpa.tistory.com/entry/NETWORK-%F0%9F%93%A1-Reverse-Proxy-Forward-Proxy-%EC%A0%95%EC%9D%98-%EC%B0%A8%EC%9D%B4-%EC%A0%95%EB%A6%AC

- https://vercel.com/templates/next.js/microfrontends
- https://nextjs.org/docs/pages/building-your-application/deploying/multi-zones

- https://kotlinworld.com/329
- https://jjjayyy.tistory.com/15
