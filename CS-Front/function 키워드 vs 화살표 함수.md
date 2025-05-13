**왜 화살표 함수는 this를 상위 스코프에서 고정할까?**

function 함수는 아래처럼 호출 방법에 따라 this가 달라집니다:

• **전역 호출**: this는 window (브라우저 환경)

• **객체의 메서드 호출**: this는 해당 객체

• **이벤트 핸들러 호출**: this는 이벤트를 발생시킨 DOM 요소

• **setTimeout 내 콜백**: this는 전역 객체 (엄격 모드에선 undefined)

`const obj = {
  value: 1,
  getValue: () => {
    console.log(this.value); // ❌ undefined
  }
};
obj.getValue(); // this는 obj가 아니라 전역 스코프의 this`

