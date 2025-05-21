
Next.js에서 SSR을 실행하는 과정

![[스크린샷 2025-05-21 오후 7.26.40.png]]

- 프론트 서버단에서 필요한 데이터를 백엔드 서버에 요청해 가져옵니다.
- 받은 데이터를 활용해 프론트 서버에서 HTML을 렌더링합니다.
- 프론트 서버에서 요청한 페이지에 필요한 HTML, CSS, JavaScript 코드를 브라우저에게 보냅니다.
- 사용자는 인터랙션 할 수 없는 HTML, CSS 코드로 그려진 화면을 보게 됩니다.
- 리액트 `hydrate` 이후 사용자는 인터랙션 가능하게 됩니다.

## hydration

hydration은 서버에서 렌더링된 HTML에 기반하여 클라이언트 측에서 JavaScript 이벤트와 상태를 연결하는 과정을 말합니다. hydration이 없다면 서버측에서 렌더링된 HTML을 무시하고 클라이언트 측에서 불필요하게 새로 렌더링이 필요한데, hydration을 통해 이러한 과정을 효율적으로 처리하게 됩니다. Next.js에서는 리액트의 [hydrate](https://react.dev/reference/react-dom/client/hydrateRoot#hydrating-server-rendered-html)를 사용합니다.

리액트는 `hydrate` 할 때, 렌더링된 콘텐츠가 서버와 클라이언트 간에 동일한 것으로 예상합니다. 개발자 모드에서 불일치가 발생하는 경우 리액트는 불일치에 대해 경고할 뿐, 일치를 위해 차이를 고쳐주지 않습니다. 이유는 대부분 불일치가 발생하는 경우가 많지 않고, 발생한다면 모든 마크업을 검증해야 해서 큰 비용이 드는 작업으로 성능상 문제가 되기 때문입니다. Next.js에서도 SSG 또는 SSR로 서버측에서 렌더링된 리액트 트리와 브라우저에서 처음 렌더링한 리액트 트리가 다른 경우 [에러를 발생](https://nextjs.org/docs/messages/react-hydration-error) 시킵니다. 또는 [Next.js hydration 으로 인한 스타일 이슈](https://fourwingsy.medium.com/next-js-hydration-%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%9D%B4%EC%8A%88-%ED%94%BC%ED%95%B4%EA%B0%80%EA%B8%B0-988ce0d939e7)를 마주하게될 수도 있습니다.

hydration 에러를 마주하지 않으려면 컴포넌트를 만들때 `window` 분기로 렌더링에 차이가 발생하지 않도록 하는 것이 좋습니다.