useInfiniteQuery는 **React Query에서 무한스크롤(페이지네이션)을 쉽게 구현할 수 있도록 도와주는 훅**이야.

일반적인 useQuery와 다르게, **“다음 페이지 존재 여부를 체크”하고, “필요할 때만 추가 데이터를 불러오는” 기능**을 제공해.

```tsx
import { useInfiniteQuery } from "@tanstack/react-query";
import axios from "axios";

  

const fetchPosts = async ({ pageParam = 1 }) => {
  const { data } = await axios.get(`/api/posts?page=${pageParam}&size=10`);
  return data;

};

  

const useInfinitePosts = () => {
  return useInfiniteQuery({

    queryKey: ["posts"],  // 캐싱 키
    queryFn: ({ pageParam }) => fetchPosts({ pageParam }),
    getNextPageParam: (lastPage, allPages) => {

      return lastPage.currentPage + 1 < lastPage.totalPages ? lastPage.currentPage + 1 : undefined;

    },
  });
};
```

### 각 옵션 설명

| 옵션               | 설명                                |
| ---------------- | --------------------------------- |
| queryKey         | **캐싱 키** (변경될 때 새로운 요청)           |
| queryFn          | **데이터를 가져오는 비동기 함수** (fetchPosts) |
| pageParam        | **현재 페이지 번호** (기본값 1)             |
| getNextPageParam | **다음 페이지 존재 여부를 확인하는 함수**         |
| staleTime        | **데이터를 신선하게 유지하는 시간** (기본값 0ms)   |
| cacheTime        | **데이터 캐싱 유지 시간** (기본값 5분)         |

---

#### **getNextPageParam의 역할**

```tsx
getNextPageParam: (lastPage, allPages) => {

  return lastPage.currentPage + 1 < lastPage.totalPages ? lastPage.currentPage + 1 : undefined;

}
```

- **다음 페이지가 있다면 nextPageParam을 반환하고, 없으면 undefined 반환.**
- 이렇게 하면 **마지막 페이지까지 도달하면 추가 요청을 보내지 않음**.

---

### **✅ useInfiniteQuery와 useQuery의 차이점**

| 비교항목              | useQuery            | useInfiniteQuery   |
| ----------------- | ------------------- | ------------------ |
| 기본 동작             | 1번 요청하면 끝           | 무한스크롤 (페이지 단위 요청)  |
| 데이터 저장 방식         | data 객체             | data.pages 배열      |
| 다음 페이지 로딩         | 직접 page 상태 관리       | fetchNextPage() 제공 |
| useEffect로 관리 필요? | 필요함 (useState 등 활용) | 불필요 (자동 관리)        |

