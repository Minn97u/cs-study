**Virtual DOM이란?**

Virtual DOM(Virtual Document Object Model)은 말 그대로 실제 DOM의 가상 버전입니다.

**JavaScript 객체 형태**로 메모리에 존재하며, 실제 DOM에 직접 접근하는 대신 이 Virtual DOM을 중간에 거쳐서 변경사항을 적용합니다.

❗ 브라우저의 DOM 조작은 매우 비싼 연산(CPU 자원 + 화면 렌더링 비용)이기 때문에, React는 이를 줄이기 위해 Virtual DOM을 도입했습니다.

---
**Virtual DOM의 동작 방식**

Virtual DOM이 상태(state)나 속성(props) 변화에 어떻게 반응하는지, 그 과정을 단계적으로 설명해볼게요.

**1. 초기에 Virtual DOM을 생성**

• React 앱이 처음 렌더링될 때, 컴포넌트 구조에 따라 **초기 Virtual DOM 트리**가 메모리 상에 만들어집니다.

**2. 상태 변경 발생**

• 사용자 이벤트(클릭, 입력 등)로 인해 state나 props가 바뀌면, React는 **새로운 Virtual DOM 트리**를 다시 생성합니다.

**3. Diffing 과정**

• 이전 Virtual DOM과 새 Virtual DOM을 비교하여 **어떤 부분이 바뀌었는지**를 찾아냅니다.

• 이 과정을 **Diffing**이라고 부릅니다.
• 단순히 문자열 비교가 아니라, 컴포넌트 구조(노드 단위)로 최적화된 알고리즘이 사용됩니다.

**4. Reconciliation (조화)**

• 변경된 부분만 **실제 DOM에 최소한으로 반영**합니다.
• 이 과정을 **Reconciliation**이라고 합니다.
• 전체 DOM을 다시 그리는 게 아니라, 바뀐 부분만 ‘꼭 필요한 만큼’ 수정하기 때문에 성능이 좋아집니다.

**5. Virtual DOM 업데이트**

• 업데이트가 끝난 후, **최신 Virtual DOM 트리**가 메모리에 저장되고, **이전 트리는 폐기**됩니다.