# **React Query란?**

React에서 서버 데이터를 가져올 때, `useState` + `useEffect`를 쓰며 관리했다고?  
**React Query**는 이 과정을 훨씬 쉽고, 효율적이며, 강력하게 만들어주는 라이브러리이다.

React Query 공식 문서를 보면 이렇게 설명되어 있다.

> “Powerful asynchronous state management for TS/JS, React, Solid, Vue and Svelte” 

조금 더 쉽게 말하자면, **React Query는 API 요청(fetch), 캐싱, 로딩 상태, 에러 처리 등을 편리하게 해주는 도구이다.**

원래는 useEffect, useState로 직접 처리하던 걸 React Query를 사용하면 정말 간단하게 만들 수 있다.

그리고 서버에서 받아온 데이터를 자동으로 관리해주기 때문에 **굳이 매번 새로 요청할 필요 없이, 캐시된 데이터를 사용**할 수도 있다.

---

## 🙋‍♀️ **왜 React Query를 써야 할까?**

리액트만 쓰면 서버 요청을 위해 이런 작업들을 매번 직접 해야만 한다.

-   `useState`로 상태 저장
-   `useEffect`로 fetch 요청
-   로딩 상태와 에러 상태 관리
-   새로고침 시 재요청 처리
-   데이터 변경 후 수동으로 갱신

👎 코드가 장황해지고 유지보수가 어려워진다.

### ✅ React Query를 사용하면?

-   `useQuery` 하나로 요청 + 로딩 + 에러 상태 처리 완료
-   데이터 **자동 캐싱**
-   네트워크 재연결 시 **자동 리페치**
-   **무한스크롤, 페이지네이션**, **POST/PUT/DELETE**도 관리
-   더 이상 서버 상태를 useState로 관리하지 않아도 됨

---

## **✨ 주요 기능 요약**

| 기능 | 설명 |
| --- | --- |
| `useQuery` | 서버에서 데이터 가져오기 |
| `useMutation` | POST, PUT, DELETE 등 데이터 변경 작업 |
| `refetch` | 수동으로 데이터 다시 불러오기 |
| 캐싱(Cache) | 이미 가져온 데이터 저장 후 재사용 |
| 자동 리페치 | 포커스 변경, 네트워크 재연결 시 자동으로 재요청 |
| 무한스크롤 | `useInfiniteQuery`로 페이지 단위 데이터 로드 |
| 로딩 & 에러 처리 | 따로 useState 없이도 isLoading, isError로 처리 가능 |

---

## **사용 방법**

### 🔧 설치

```
npm i @tanstack/react-query @tanstack/react-query-devtools
```

## 기본 예제: `useQuery`

```
import { useQuery } from "@tanstack/react-query";
import axios from "axios";

const fetchTodos = async () => {
  const { data } = await axios.get("/api/todos");
  return data;
};

const TodoList = () => {
  const { data, isLoading, isError, refetch } = useQuery({
    queryKey: ["todos"],
    queryFn: fetchTodos,
  });

  if (isLoading) return <p>로딩 중...</p>;
  if (isError) return <p>에러가 발생했습니다.</p>;

  return (
    <>
      <ul>
        {data.map((todo) => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
      <button onClick={refetch}>다시 불러오기</button>
    </>
  );
};
```

---

## **refetch란?**

React Query는 데이터를 캐싱하면서도, 언제 다시 가져와야 할지를 자동으로 판단한다.  
그 기준이 되는 게 바로 stale 상태이다.

**✏️ 기본적으로 refetch가 일어나는 경우는?**

1\. refetchOnWindowFocus: 탭에 다시 포커스되었을 때  
2\. refetchOnMount: 컴포넌트가 마운트될 때  
3\. refetchOnReconnect: 네트워크가 끊겼다가 다시 연결될 때

이 옵션들은 기본값이 true. 즉, 아무 설정 없이도 자동으로 refetch가 된다.

```
const { refetch } = useQuery(...);
<button onClick={refetch}>최신 데이터 불러오기</button>
```

```
useQuery({
  queryKey: ["todos"],
  queryFn: fetchTodos,
  refetchOnWindowFocus: true,      // 탭 전환 후 돌아왔을 때
  refetchInterval: 10000,          // 10초마다 자동 요청
});
```

---

#### **StaleTime** : 신선한 데이터 유지 시간

staleTime은 **데이터가 신선(fresh)하게 유지되는 시간**을 의미

기본값은 0ms이고, 이 시간이 지나면 React Query는 데이터를 “오래됨(stale)“으로 간주한다.

```
useQuery({
  queryKey: ['users'],
  queryFn: fetchUsers,
  staleTime: 1000 * 60 * 5, // 5분 동안은 refetch 하지 않음
});
```

뉴스나 공지사항처럼 자주 바뀌지 않는 데이터는 staleTime을 길게 설정하는 게 좋다.

---

#### **데이터 강제 업데이트: invalidateQueries**

백그라운드에서 새로운 데이터가 생겼는데 캐시된 데이터 때문에 안 보일 때,  
React Query에게 “이 데이터는 무효야, 다시 가져와!“라고 말할 수 있다.

```
import { useQueryClient } from "@tanstack/react-query";

const client = useQueryClient();

<button onClick={() => {
  client.invalidateQueries({ queryKey: ['users'] });
}}>새로고침</button>
```

---

#### **cacheTime**: 캐시 보존 시간

cacheTime은 캐시된 데이터가 메모리에 얼마나 남아 있을지를 정하는 시간

```
useQuery({
  queryKey: ['users'],
  queryFn: fetchUsers,
  cacheTime: 1000 * 60 * 30, // 30분
});
```

캐시가 삭제되지 않는 한, 같은 쿼리 키로 요청할 경우 기존 데이터를 바로 보여준다.

---

#### ✅ 언제 사용하면 좋을까?

-   서버에서 데이터를 자주 가져오는 앱
-   최신 상태 유지가 중요한 서비스 (알림, 거래 내역 등)
-   무한스크롤, 정렬, 필터 기능이 있는 리스트
-   서버 상태와 클라이언트 상태를 분리하고 싶을 때

---

#### ✅  결론

React Query는 다음과 같은 장점이 있다

• 복잡한 상태 관리 코드 없이 서버 데이터를 가져올 수 있음  
• 자동 캐싱과 자동 갱신 덕분에 성능 향상  
• 코드가 간결하고 재사용 가능성이 높음  
• Devtools, refetch, invalidate, useMutation, 무한