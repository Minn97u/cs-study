**💡 호이스팅이란?**

**“변수와 함수 선언이 코드 실행 전, 스코프의 최상단으로 끌어올려지는 현상”**

-   자바스크립트는 실행 전에 전체 코드를 파싱(Parsing) 하면서, 변수와 함수의 선언부를 먼저 메모리에 등록한다.
-   이를 통해 코드에서 **선언보다 앞선 위치에서도 변수/함수에 접근이 가능**해지는 것

예시

```
console.log(a); // undefined
var a = 10;
```

 코드는 내부적으로 이렇게 동작합니다

```
var a;           // 선언만 끌어올려짐 (호이스팅)
console.log(a);  // undefined
a = 10;          // 실제 할당
```

---

 **함수 호이스팅**

**함수 선언문 (function foo() {})**

-   선언 + 초기화까지 함께 호이스팅
-   코드에서 선언보다 위에서 호출해도 작동

```
greet(); // Hello!

function greet() {
  console.log('Hello!');
}
```

**함수 표현식 (var foo = function() {})**

-   변수 취급: 선언만 호이스팅되고 초기화는 안 됨
-   초기화 전 호출 시 TypeError 또는 undefined is not a function

```
sayHi(); // TypeError

var sayHi = function() {
  console.log('Hi!');
};
```

var 변수는 호이스팅되지만, 값은 undefined 이므로 함수처럼 호출하면 에러 발생

---

**호이스팅의 발생 시점: 실행 컨텍스트 생성**

자바스크립트는 코드를 실행할 때 다음 두 단계를 거치게되는데

1. **파싱 단계 (Creation Phase)**

변수, 함수 선언을 **Lexical Environment**에 등록

이 시점에 호이스팅 발생

2. **실행 단계 (Execution Phase)**

실제 코드가 한 줄씩 실행됨

변수 할당, 함수 호출 등 처리

---

**호이스팅이 문제가 되는 이유**

-   코드를 위에서 아래로 읽는 개발자의 직관을 해침
-   선언 전 변수 참조가 가능해서 예상치 못한 undefined 값 발생
-   복잡한 버그를 유발하기 쉬움

\-> 이를 보완하기 위해 ES6 이후 let, const 도입 및 TDZ 개념 적용