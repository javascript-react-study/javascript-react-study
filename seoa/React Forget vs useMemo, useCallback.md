# React Forget vs useMemo, useCallback

# React Forget의 등장

- **"자동으로 메모하는 컴파일러"**인 리액트 Forget은 리액트 커뮤니티를 강타한 혁신적인 최신 도구
- [2021년 리액트 컨퍼런스](https://www.youtube.com/watch?v=lGEMwh32soc)에서 공개된 이 컴파일러는 `React.useMemo` 또는 `React.useCallback`의 필요성을 감지하여 컴포넌트 리렌더링 성능을 향상시킴
- 컴파일러는 이러한 구조와 유사한 동작을 하는 코드를 삽입하여 리액트 애플리케이션의 성능을 최적화
- 리액트 팀의 핵심 멤버인 [Dan Abramov](https://github.com/gaearon)는 컴파일러는 `useMemo()`의 결과를 계산할 뿐만 아니라 컴포넌트가 반환한 결과물인 리액트 엘리먼트 객체도 메모화하기 때문에, `React.memo()`가 더 이상 필요하지 않을 수도 있다고 이야기 했음

## React Forget의 매커니즘

- 리액트 Forget의 코어는 바벨과 거의 완전히 분리되어 있으며, 핵심 컴파일러 API는 단순히 이전 AST(abstract syntax tree)를 입력하고 새 AST를 출력하는 방식
    - 추상 구문 트리(abstract syntax tree, AST): [프로그래밍 언어](https://ko.wikipedia.org/wiki/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D_%EC%96%B8%EC%96%B4)로 작성된 [소스 코드](https://ko.wikipedia.org/wiki/%EC%86%8C%EC%8A%A4_%EC%BD%94%EB%93%9C)의 추상 [구문](https://ko.wikipedia.org/wiki/%ED%86%B5%EC%82%AC%EB%A1%A0) 구조의 [트리](https://ko.wikipedia.org/wiki/%ED%8A%B8%EB%A6%AC)
        - 이 트리의 각 노드는 소스 코드에서 발생되는 구조를 나타내며, 구문이 추상적이라는 의미는 실제 구문에서 나타나는 모든 세세한 정보를 나타내지는 않는다는 것을 의미
- 이는 원본 로케이션 데이터를 유지하면서 달성되며, 내부적으로 컴파일러는 저수준의 의미 분석을 수행하기 위해 사용자 정의 코드 표현 및 변환 파이프라인을 사용

## 현재 개발 상황 및 향후 계획

- 리액트 Forget의 개발자인 Joe Savona에 따르면 2023년 현재 리액트 Forget은 여전히 활발하게 개발 중이라고 함
- 프로젝트가 상당한 발전을 이루었지만 아직 출시할 준비가 되지 않았다고 이야기 했으며, Savona는 대중에게 공개하기 전에 최고 수준의 품질, 문서화, 지원, 버그 수정을 보장하기 위한 팀의 노력을 강조
- 향후 계획에 대해 논의할 때 궁극적으로 리액트 Forget을 오픈소스화하는 것이 목표라고 언급했으나 오픈소스 버전의 정확한 출시 날짜는 강연 중에 공개하지 않음
- 품질과 커뮤니티 지원에 대한 높은 기준을 충족하여 오픈소스화 하고싶다는 것
- 사용법과 관련하여 Joe Savona는 리액트 Forget이 빌드 파이프라인에 통합되도록 설계되었다고 강조
- 리액트의 안정적인 버전에 필수 런타임 API가 포함되면 개발자는 리액트 Forget을 바벨 플러그인으로 설치할 수 있음
- 이 접근 방식은 리액트 Forget이 기존 리액트 프로젝트와 원활하게 통합될 수 있도록 보장

# **useMemo 및 useCallback의 현재 필요성**

- 리액트 Forget의 중요성을 이해하려면 먼저 현재 리액트 생태계에서 `useMemo`와 `useCallback`의 필요성을 이해해야 함

## **useMemo의 역할**

- `useMemo`는 리렌더링 사이의 계산 결과를 캐시할 수 있는 리액트 Hook
- 캐시하려는 값을 계산하는 함수와 의존성 목록 두 가지 인수를 받음

```jsx
import { useMemo } from 'react';

function TodoList({ todos, tab }) {
  const visibleTodos = useMemo(
    () => filterTodos(todos, tab),
    [todos, tab]
  );
  // ...
}
```

- 이 예시에서 `useMemo`는 `TodoList` 컴포넌트의 불필요한 리렌더링을 생략하여 성능을 최적화하는 데 도움이 됨

## **useCallback의 필요성**

- `useCallback`은 콜백 함수의 메모화된 버전(의존성 중 하나라도 변경되었을 때에만 변경되는)을 반환
- 참조 값의 동일 여부에 의존하는 자식 컴포넌트에 콜백을 전달할 때 불필요한 렌더링을 방지하기 위해 사용할 수 있음

```jsx
import { useCallback } from 'react';

function TodoList({ todos, tab }) {
  const handleTodoClick = useCallback(
    (id) => {
      // 클릭 이벤트 처리
    },
    [todos, tab] // 의존성
  );

  // ...
}
```

- 이 경우, `useCallback`은 `todos`이나 `tab`이 변경되지 않는 한 모든 렌더링에서 `handleTodoClick` 함수가 변경되지 않도록 보장하여 불필요한 리렌더링을 방지함

## **useMemo 및 useCallback의 문제점**

- `useMemo`와 `useCallback`은 성능을 최적화하는 훌륭한 도구이지만 코드에 복잡성을 추가하고 올바르게 사용하기 어려울 수 있음
- 의존성 배열을 올바르게 지정하지 않으면 혼란스러운 버그와 성능 문제가 발생할 수 있고, 코드를 읽고 이해하기 어려울 수 있음

# **리액트 Forget의 약속**

- 리액트 Forget은 메모화 프로세스를 자동화하여 이러한 문제를 해결하는 것을 목표로 함
- 새로운 컴파일러를 사용하면 개발자가 수동으로 `useMemo`나 `useCallback`을 사용할 필요가 없음
- 대신 컴파일러가 메모화가 필요한 시점을 자동으로 판단하여 적용하므로 개발자의 정신적 부담이 줄어들고 코드를 읽고 이해하기 쉬워짐

# **결론: 미래를 위한 준비**

- 리액트 개발자는 리액트 생태계의 최신 개발과 기능을 계속 업데이트하는 것이 중요함
- 곧 출시될 리액트 Forget은 매우 획기적일 것이며, 수동으로 메모할 필요 없이 성능 좋은 리액트 컴포넌트를 더 쉽게 작성할 수 있게 해줄 것임
- 이 새로운 도구가 출시되기를 기대하지만, 현재 사용 중인 `useMemo`와 `useCallback`과 같은 도구를 이해하고 숙달하는 것은 여전히 필수적임

---

참고 사이트

- [https://velog.io/@lky5697/how-react-forget-will-make-react-usememo-and-usecallback-hooks-absolutely-redundant](https://velog.io/@lky5697/how-react-forget-will-make-react-usememo-and-usecallback-hooks-absolutely-redundant)
- [https://dev.to/usulpro/how-react-forget-will-make-react-usememo-and-usecallback-hooks-absolutely-redundant-4l68](https://dev.to/usulpro/how-react-forget-will-make-react-usememo-and-usecallback-hooks-absolutely-redundant-4l68)