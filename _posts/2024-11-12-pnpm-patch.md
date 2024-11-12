---
layout: post
title: Something wrong with my dependencies - Pnpm patch
tags: [pnpm, package manager, JavaScript, Node.js, dependency]
excerpt_separator: <!--more-->
---

프로젝트를 진행하면서 새로운 dependency를 도입하거나 기존 dependency의 버전을 바꾸는 경우, dependency의 내부 동작이 의도와는 다르게 동작하여 문제가 발생하는 경우가 있습니다.

이럴 때, `pnpm patch`를 이용할 수 있습니다.

![package-patching]({{ "../assets/img/aboutDependency/package-patch.png" | relative_url }})

<!--more-->

# pnpm patch

[pnpm patch](https://pnpm.io/ko/cli/patch) 에 대해 알아보겠습니다.

현 포스팅에선 예시로 `is-odd`라는 매우 간단한 dependency를 변경해보겠습니다.

## pnpm patch가 필요한 경우

dependency가 문제가 있는 경우 `/node_modules`에 있는 코드에 직접 접근해 수정하여 디버깅하거나 해결 할 수 있습니다.

하지만, 이렇게 직접 수정한 코드는 제 local 프로젝트에만 적용되기 때문에, 다른 프로젝트에 적용하거나 다른 개발자와 공유하기 어렵습니다. (보통 `/node_modules`는 `.gitignore`에 포함되어 있기 때문에 git에 저장되지 않습니다.)

이럴 때 `pnpm patch`를 이용하면, dependency를 수정한 내용을 `.patch` 파일로 하여금, 해당 dependency가 install 될 때마다 dependency 코드를 수정할 수 있습니다.

## pnpm patch

지금부터 `pnpm patch`를 이용해 dependency를 수정하는 방법을 알아보겠습니다.

먼저 바꾸고 싶은 dependency를 선정합니다.

그리고 다음과같이 명령어를 입력합니다.

```bash
$ pnpm patch <pkg name>@<version>
```

그럼 다음과 같은 내용이 나옵니다.

![pnpm-command]({{ "../assets/img/aboutDependency/pnpm-command.png" | relative_url }})

## pnpm patch-commit

이제 pnpm에서 알려준 path에 접근해 코드를 변경합니다.

그리고 pnpm이 준 `pnpm patch-commit` 커맨드를 실행합니다.

`pnpm patch-commit`와 함께 직접 patch한 dependency 위치를 함께 줍니다.

```bash
$ pnpm patch-commit "/Users/{유저ID}/me/patch-test/node_modules/.pnpm_patches/is-odd@3.0.1"
```

## patch 결과

patch 결과 `<pkg name>@<version>.patch` 파일이 생성되며, .patch 파일의 내용에 맞춰 `<pkg name>@<version>`가 install 될 때마다 내부 코드를 갱신합니다.

![patch-result]({{ "../assets/img/aboutDependency/patch-result.png" | relative_url }})

현 포스팅에선 예시로 다뤘던 `is-odd@3.0.1`의 코드가 다음과 같이 변경됩니다.

```bash
diff --git a/index.js b/index.js
index 79d1f22a8e7a27efb8841bb83cb682ea1ff3a59c..56d53e8ceb4f353e88206f4977580da734a575e3 100644
--- a/index.js
+++ b/index.js
@@ -20,6 +20,10 @@ module.exports = function isOdd(value) {
   if (!Number.isSafeInteger(n)) {
     throw new Error('value exceeds maximum safe integer');
   }
-  return (n % 2) === 1;
+  const result = (n % 2) === 1;
+  return {
+    result,
+    text: result ? '홀수' : '짝수',
+  };
 };
```

is-odd 함수에서 주는 return 값이 달라졌습니다.

# 활용 예시

최근 프로젝트에서 emotion과 pandacss 함께 사용하고 있습니다. 궁극적으로 기존에 사용하던 emotion.js를 pandacss로 마이그레이션 하는 것이 목표입니다.

여기서 pandacss는 css layer를 적극 활용하는 라이브러리이지만, emotion은 그렇지 않습니다.

이로 인해 emotion으로 생성된 스타일이 pandacss로 생성된 스타일보다 우선순위가 높아지거나 하는 경우가 생겨 저희가 의도한 대로 스타일이 적용되지 않는 문제가 발생했습니다.

따라서, `pnpm patch`를 이용해 emotion에서 생성하는 모든 스타일을 css layer에 넣어 css를 생성하도록 수정하였고, pandacss의 css layer보다 후순위로 두어 문제를 해결하였습니다.

# Conclusion

`pnpm patch`는 어디까지나 임시 조치로 유용하게 사용될 수 있지만, 매번 dependency를 patch하는 것은 권장되지 않습니다.

dependency에 문제가 있거나 추가 혹은 수정이 되었으면 하는 점이 있다면, 해당 dependency의 issue를 오픈하여 수정을 요청하거나, 직접 수정하여 프로젝트에 기여하는 것이 더 좋은 방법이라고 생각합니다.

감사합니다.
