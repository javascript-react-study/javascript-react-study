# Context

# Context란?

- **React 컴포넌트 트리 안에서 전역적(global)이라고 볼 수 있는 데이터를 공유할 수 있도록 고안된 방법**
    
    **→ ‘React 컴포넌트에서 props가 아닌 또 다른 방식으로 컴포넌트 간에 값을 전달하는 방법’이라고 접근하는 것이 좋음**
    
- 일반적인 React 애플리케이션에서 데이터는 위에서 아래로(즉, 부모로부터 자식에게) props 를 통해 전달됨
- 여러 컴포넌트들에게 전달해야 하는 props의 경우 이 과정이 번거로움
- context는 트리 단계마다 명시적으로 props를 넘겨주지 않아도 많은 컴포넌트가 값을 공유하도록 할 수 있음
- context 내부에 포함된 컴포넌트들이 값의 일부에만 관심이 있더라도 re-render 되므로 성능 문제가 발생할 수 있음

## context를 언제 사용해야 할까?

- context는 앱의 모든 컴포넌트에서 사용할 수 있는 데이터를 전달할 때 유용함
- 데이터 종류
    - 테마 데이터(다크 모드 혹은 라이트 모드)
    - 사용자 데이터(현재 인증된 사용자)
    - 로케일 데이터(언어 혹은 지역)
- **props drilling을 피하고 싶고, 낮은 규모와 자주 업데이트할 필요가 없는 경우, 단순히 값을 공유할 때 사용하는 것이 좋음**

## context가 뭘 해결해 줄까?

- 모든 컴포넌트에 props를 사용하는 것을 막을 수 있어 props drilling을 피할 수 있음
    - props drilling이란?
        - 중첩된 여러 계층의 컴포넌트에게(해당 props를 사용하지 않는 컴포넌트들에게까지) props를 전달해 주는 것

## context를 어떻게 사용할까?

- `createContext` 메서드를 사용해 context를 생성
    
    ```jsx
    import { createContext } from 'react';
    
    const MyContext = creatContext();
    ```
    
- Context 객체 안에 들어있는 Provider라는 컴포넌트로 전달하고 싶은 컴포넌트를 감싸줌
    
    ```jsx
    function App() {
    	return (
    		<MyContext.Provider value="Hello World">
    			<AwsomeComponents />
    		<MyContext.Provider />
    	);
    }
    ```
    
    - 컴포넌트간에 공유하고자 하는 값을 `value`라는 props로 설정하면 자식 컴포넌트들에서 해당 값에 바로 접근할 수 있음
- 원하는 컴포넌트에서 `useContext` Hook을 사용하여 context에 넣은 값에 바로 접근할 수 있음
    
    ```jsx
    import { createContext, useContext } from 'react';
    
    function Message() {
    	const MyContext = creatContext(MyContext);
    	return <div>Received: {value}</div>;
    }
    ```
    
    - Hook의 인자에는 `createContext`로 만든 MyContext를 넣음

### Context 사용 예시

- AwesomeComponent에서 내부의 각 컴포넌트들에게 props를 설정해주는 것이 아니라, 각 컴포넌트에서 원하는 값을 `useContext`로 가져올 수 있음
    
    ```jsx
    import { createContext, useContext } from 'react';
    const MyContext = createContext();
    
    function App() {
      return (
        <MyContext.Provider value="Hello World">
          <AwesomeComponent />
        </MyContext.Provider>
      );
    }
    
    function AwesomeComponent() {
      return (
        <div>
          <FirstComponent />
          <SecondComponent />
          <ThirdComponent />
        </div>
      );
    }
    
    function FirstComponent() {
      const value = useContext(MyContext);
      return <div>First Component says: "{value}"</div>;
    }
    
    function SecondComponent() {
      const value = useContext(MyContext);
      return <div>Second Component says: "{value}"</div>;
    }
    
    function ThirdComponent() {
      const value = useContext(MyContext);
      return <div>Third Component says: "{value}"</div>;
    }
    
    export default App;
    ```
    

- context가 여러 컴포넌트에서 사용되고 있다면 커스텀 Hook을 만들어서 사용하는 것도 좋은 방법임
    
    ```jsx
    import { createContext, useContext } from 'react';
    const MyContext = createContext();
    
    function useMyContext() {
      return useContext(MyContext);
    }
    
    function App() {
      return (
        <MyContext.Provider value="Hello World">
          <AwesomeComponent />
        </MyContext.Provider>
      );
    }
    
    function AwesomeComponent() {
      return (
        <div>
          <FirstComponent />
          <SecondComponent />
          <ThirdComponent />
        </div>
      );
    }
    
    function FirstComponent() {
      const value = useMyContext();
      return <div>First Component says: "{value}"</div>;
    }
    
    function SecondComponent() {
      const value = useMyContext();
      return <div>Second Component says: "{value}"</div>;
    }
    
    function ThirdComponent() {
      const value = useMyContext();
      return <div>Third Component says: "{value}"</div>;
    }
    
    export default App;
    ```
    

## Context와 전역 상태 관리 라이브러리

- 과거에는 context가 불편해서 전역 상태 관리 라이브러리를 사용하는 것이 당연시 여겨졌지만, 이제는 사용하기 편해져서 단순히 전역적인 상태를 관리하기 위함이라면 더 이상 사용해야 할 이유가 없음
- 전역 상태 관리 라이브러리는 결국 상태 관리를 조금 더 쉽게 하기 위해서 사용하는 것이며 취향에 따라 선택해서 사용하면 됨

### 상태 관리 라이브러리

- 상태 관리 라이브러리는 상태 관리를 더욱 편하고, 효율적으로 할 수 있게 해주는 기능들을 제공해주는 도구
    - 이에 비교하면, context는 전역 상태 관리를 할 수 있는 수단일 뿐임
- Redux
    - 액션과 리듀서라는 개념을 사용하여 상태 업데이트 로직을 컴포넌트 밖으로 분리할 수 있게 해줌
    - 상태가 업데이트 될 때 실제로 의존하는 값이 바뀔 때만 컴포넌트가 리렌더링 되도록 최적화
    - context를 쓴다면, 각기 다른 상태마다 새롭게 context를 만들어 주어야하는 과정을 생략할 수 있어 편리함
- MobX
    - Redux와 마찬가지로 상태 업데이트 로직을 컴포넌트 밖으로 분리할 수 있게 해줌
    - 함수형 리액티브 프로그래밍 방식을 도입하여 mutable한 상태가 리액트에서도 잘 보여지게 해줌
    - 상태 업데이트 로직을 더욱 편하게 작성할 수 있게 해주고, 최적화도 잘 해줌
- Recoil, Jotai, Zustand
    - context를 만드는 과정을 생략하고 Hook 기반으로 아주 편하게 전역 상태 관리를 할 수 있게 해줌
    

---

출처

- [https://ko.legacy.reactjs.org/docs/context.html](https://ko.legacy.reactjs.org/docs/context.html)
- [https://react.dev/reference/react/useContext#usecontext](https://react.dev/reference/react/useContext#usecontext)
- [https://velog.io/@velopert/react-context-tutorial](https://velog.io/@velopert/react-context-tutorial)
- [https://github.com/frontend-article-study/frontend-article-study/blob/main/eunhee/contextAPI.md](https://github.com/frontend-article-study/frontend-article-study/blob/main/eunhee/contextAPI.md)