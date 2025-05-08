React를 공부하다 보면 반드시 마주치는 개념 중 하나가 바로 **Virtual DOM이다.** 이 글에서는 Virtual DOM이 **왜 등장하게 되었고**, **어떻게 동작하며**, **언제 사용하면 좋은지**까지 예시를 통해 쉽게 설명해보려고 한다.


---


✏️ **Virtual DOM이란?**


Virtual DOM(Virtual Document Object Model)은 말 그대로 **실제 DOM의 가상 버전이다.**

**JavaScript 객체 형태**로 메모리에 존재하며, 실제 DOM에 직접 접근하는 대신 이 Virtual DOM을 중간에 거쳐서 변경사항을 적용하게 된다.

\- 브라우저의 DOM 조작은 매우 비싼 연산(CPU 자원 + 화면 렌더링 비용)이기 때문에, React는 이를 줄이기 위해 Virtual DOM을 도입했다.

​

---

​

✏️ **Virtual DOM의 동작 방식**

​

Virtual DOM이 상태(state)나 속성(props) 변화에 어떻게 반응하는지, 그 과정을 단계적으로 설명하고자 한다.

**1\. 초기에 Virtual DOM을 생성**

React 앱이 처음 렌더링될 때, 컴포넌트 구조에 따라 **초기 Virtual DOM 트리**가 메모리 상에 만들어진다.

**2\. 상태 변경 발생**

사용자 이벤트(클릭, 입력 등)로 인해 state나 props가 바뀌면, React는 **새로운 Virtual DOM 트리**를 다시 생성한다.

​

**3\. Diffing 과정**

이전 Virtual DOM과 새 Virtual DOM을 비교하여 **어떤 부분이 바뀌었는지**를 찾아낸다.
이 과정을 **Diffing**이라고 부른다.

단순히 문자열 비교가 아니라, 컴포넌트 구조(노드 단위)로 최적화된 알고리즘이 사용된다.


**4\. Reconciliation (조화)**

변경된 부분만 **실제 DOM에 최소한으로 반영한다.**
이 과정을 **Reconciliation**이라고 하며
전체 DOM을 다시 그리는 게 아니라, 바뀐 부분만 ‘꼭 필요한 만큼’ 수정하기 때문에 성능이 좋아진다.


**5\. Virtual DOM 업데이트**
업데이트가 끝난 후, **최신 Virtual DOM 트리**가 메모리에 저장되고, **이전 트리는 폐기된다.**


---

​

✍🏻 예시

​

버튼을 클릭해서 숫자를 1씩 증가시키는 간단한 카운터 예제를 가정해봅시다.

```

function Counter() {

  const [count, setCount] = useState(0);

  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;

}

```

​

**흐름**

1.  최초 렌더링 시 Virtual DOM에 <button>Count: 0</button>이 저장됨.

2.  버튼 클릭 → count가 1로 변경됨 → 새로운 Virtual DOM 생성.

3.  이전과 비교 → “텍스트가 0에서 1로 바뀌었네!” → 변경점 탐지(Diffing).

4.  해당 텍스트 노드만 실제 DOM에 반영(Reconciliation).

​

즉, 버튼 전체를 다시 렌더링하는 것이 아니라, 0 → 1로 바뀐 텍스트만 바뀌는 것이다.

​

---

​

**💡 성능 최적화를 위한 Batch Update**

​

React는 상태나 props가 한 번에 여러 개 변경될 수 있다는 점을 고려하여, 배치 업데이트(Batch Update)를 지원한다.

​

즉, 10개의 상태가 연속으로 바뀌더라도 매번 Virtual DOM 비교 → 실제 DOM 반영을 하지 않고, **한 번에 모아서 처리하는 것이다.**

​

이러한 전략 덕분에 렌더링 성능을 극대화할 수 있다.

​

---

​

**Virtual DOM이 무조건 좋은가??**

​

오히려 단순한 웹페이지라면 Virtual DOM이 **오버헤드**가 될 수도 있습니다.

​

| 상황 | **Virtual DOM 사용 이점** |

| --- | --- |

| 상태 변경이 거의 없음 | 오히려 메모리 낭비 |

| 복잡한 트리구조 + 상태 변화 잦음 | 업데이트 최적화로 성능이 향상된다. |

| 대규모 SPA ( 단일 페이지 어플리 케이션) | 유지보수성과 렌더링 속도가 증가한다. |

​

따라서, 어플리케이션의 복잡도와 규모에 따라 도입 여부를 고려하는 것이 좋다.