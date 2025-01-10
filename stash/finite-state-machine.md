## Feedback

Xstate를 이용한 상태머신을 쓰면서 좋은것들이 많긴했디만 개인적으로 좋았던 것은 Effect를 명시적이고  안전하게 관리 할 수 있던 것인데, 예시는 그냥 reducer만으로도 가능한 심플한 상태 관리라서 아쉬워요.

어떤 이벤트 발생후 useEffect의 dependency에 넣어둔 값이 변경되면 내부의 함수가 동작하잔아요

이게 하나만있으면 추적하는 것이 어렵지 않겠지만 여러개가 되면 이 effect가 다른 effect의 트리거가 되면서 추적하기가 굉장히 어려워져요

그걸 xstate에서는 특정 state에 도달할때 entry이든 그 state에서 나가는 exit이든  동작하게 만들면 이벤트가 언제 동작하는지 사람이 추적하기가 쉬워서 굉장히 코드를 읽는 게 편했어요

PaymentSend: {
      invoke: {
        src: 'fetchPayment', // 비동기 작업 호출
        onDone: {
          target: 'Init', // 작업 완료 시 Init으로 이동
        },
        onError: {
          target: 'error', // 에러 발생 시
        },
      },
  after: {
        3000: { target: 'Loading' }, // 3초 후 Loading 상태로 변경하여 UI 표시
      }
}

이런식으로 특정 상태에 도달하면 미리 선언해둔 action이나 service가 실행되도록 하는 것.
= 특정 callback이 특정한 상태에만 의존성을 가져서 가독성이 올라간다.
useEffect의 의존성이 늘어나면 가독성이 안좋아지는 것처럼
