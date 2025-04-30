비동기 작업을 처리하다 보면 콜백 함수를 계속 중첩하게 된다.

​

특히 순서를 보장하면서 여러 작업을 해야 할 땐, 코드가 이렇게 돼버린다

​

```

getUser(function(user) {

  getPosts(user.id, function(posts) {

    getComments(posts[0].id, function(comments) {

      console.log('모든 데이터 완료!');

    });

  });

});

```

​

이렇게 되면 문제점이 많이 생기는데

​

-   들여쓰기가 깊어진다.

-   에러 핸들링이 어려워진다.

-   디버깅 난이도가 올라간다

​

이런 구조를 **콜백 지옥(callback hell)** 이라고 부른다.

​

---

​

**✏️ 그래서 등장한 Promise**

​

**Promise**는 비동기 작업의 상태(pending, fulfilled, rejected)를 관리하는 객체다.

​

then과 catch를 이용해서 **비동기 흐름을 체이닝**할 수 있도록 도와준다.

​

> 비동기 처리가 진행중이면 pending, 성공이면 fulfilled, 실패면 rejected라는 상태

​

```

getUser()

  .then(user => getPosts(user.id))

  .then(posts => getComments(posts[0].id))

  .then(comments => console.log('모든 데이터 완료!'))

  .catch(error => console.error(error));

```

​

-   .then()으로 깔끔하게 순서 연결

-   .catch() 하나로 에러 일괄 처리

​

then과 catch의 체이닝을 통해 보다 간결하게 표현할 수 있도록, ES6에서 새로 도입되었다.

​

---

​

**✏️ async/await** 

​

async/await은 Promise 기반 문법이다.

​

async 함수 안에서 await 키워드를 사용하면, **Promise가 끝날 때까지 기다렸다가 다음 줄로 넘어간다.**

​

마치 동기 코드처럼 작성할 수 있어 **가독성**이 훨씬 좋다.

​

```

async function loadData() {

  try {

    const user = await getUser();

    const posts = await getPosts(user.id);

    const comments = await getComments(posts[0].id);

    console.log('모든 데이터 완료!');

  } catch (error) {

    console.error(error);

  }

}

```

​

**💡 async/await을 사용할 때 주의할 점**

​

**1\. 반드시 try-catch로 감싸기**

​

await는 내부적으로 Promise를 사용하기 때문에,

​

실패한 Promise가 **rejected** 상태가 되면 에러를 던진다.

​

```

try {

  const result = await mightFail();

} catch (e) {

  console.error(e);

}

```

​

 **2\. 병렬 처리할 땐 Promise.all**

​

```

// 순차 실행 (비효율적)

await work1();

await work2();

await work3();

​

// 병렬 실행 (빠름)

await Promise.all([work1(), work2(), work3()]);

```

​

await는 코드 실행을 멈추기 때문에, **불필요한 순차 실행은 성능 저하**를 유발할 수 있다.

​

**async/await + fetch 실습 예제**

​

```

async function fetchUserData(userId) {

  try {

    const response = await fetch(`https://mingyuzzang.com/users/${userId}`);

    if (!response.ok) {

      throw new Error('네트워크 응답에 문제가 있습니다.');

    }

    const data = await response.json();

    console.log('사용자 데이터:', data);

  } catch (error) {

    console.error('에러 발생:', error);

  } finally {

    console.log('요청이 끝났습니다.');

  }

}

​

fetchUserData(1);

```

​

-   await fetch(): 서버 응답을 기다린다.

-   await response.json(): JSON 데이터 파싱을 기다린다.

-   try-catch-finally로 에러와 마무리까지 관리한다.

​

---

​

**✏️ try-catch-finally 패턴 정리**

​

async/await을 쓸 때,

​

**try-catch로 에러를 핸들링**하고, **finally로 후처리를 깔끔하게** 정리할 수 있다.

​

```

async function example() {

  try {

    const result = await someFunction();

    console.log('성공:', result);

  } catch (error) {

    console.error('실패:', error);

  } finally {

    console.log('무조건 실행: 정리 또는 로딩 종료');

  }

}

```

​

-   **try**: 정상 실행할 코드

-   **catch**: 에러 발생 시 처리할 코드

-   **finally**: 성공하든 실패하든 마지막에 무조건 실행할 코드

​

>  finally는 특히  

>   

> **\- 로딩 스피너 숨기기**  

> **\- 파일 닫기**  

> **\- DB 연결 끊기**  

>   

> 같은 **“마지막 정리 작업”** 할 때 정말 유용하다고 한다.