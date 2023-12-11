> 저는 리엑트로 코딩을 할때 리엑트의 다양한 기능을 모르고 useState만 남발하는 useState무새 였어요
> 코딩을하다 리엑트에 대해 궁금한게 생겨서 검색하다 보면 useRef라는 훅을 자주 볼수 있었고
> 그것은 주로 Input을 관리하는데 사용된다는 것을 알수 있었는데 둘의 차이를 정확히 몰랐습니다
> 이번 기회에 둘의 차이를 확실히 짚고 둘 중에 무엇을 쓰는게 불필요한 렌더링을 줄일수 있을지 결정지어보고자 합니다

# useState

---

리엑트에는 클래스형컴포넌트와 함수형컴포넌트가 있고 대중적으로는 함수형컴포넌트를 주로 사용하는 추세입니다

```jsx
const [email, setEmail] = useState('');
//[상태유지 값, 그 값을 갱신하는 함수]
```

상태유지 값을 이용해 해당 값을 유저에게 보여줄수 있고,
그 값을 갱신하는 함수를 통하여 유저에게 그 값을 변경할수 있도록 해줄수 있습니다

state는 무언가를 보여주는 예를 들자면 p태그 같은 곳에 쓰이고
setState는 유저의 입력을 요구한다거나 유저가 웹 내애세 어떠한 행위를 할 때 state를 변경해주는 함수인 것입니다 !

## 버튼에서 사용 예시

---

```jsx
import { useState } from 'react';
import reactLogo from './assets/react.svg';
import './App.css';

function App() {
  const [count, setCount] = useState(0);

  return (
    <div className='App'>
      <div>
        <a href='https://reactjs.org' target='_blank' rel='noreferrer'>
          <img src={reactLogo} className='logo react' alt='React logo' />
        </a>
        <a href='https://vitejs.dev' target='_blank' rel='noreferrer'>
          <img src='/vite.svg' className='logo' alt='Vite logo' />
        </a>
      </div>
      <h1>React + Vite</h1>
      <h2>On CodeSandbox!</h2>
      <div className='card'>
        <button onClick={() => setCount((count) => count + 1)}>count is {count}</button>
        <p>
          Edit <code>src/App.tsx</code> and save to test HMR.
        </p>

        <p>
          Tip: you can use the inspector button next to address bar to click on components in the
          preview and open the code in the editor!
        </p>
      </div>
      <p className='read-the-docs'>Click on the Vite and React logos to learn more</p>
    </div>
  );
}

export default App;
```

![](https://velog.velcdn.com/images/qwzx16/post/e562d406-25f1-47a4-93f2-ab8ad0c8a46b/image.gif)

버튼을 클릭할때 값이 더해지고 그것이 화면에 즉시 렌더링되어 우리에게 보여준다는 것을 확인할수 있습니다.

## 인풋에서 사용 예시

---

```jsx
import { useEffect, useState } from 'react';
import './App.css';

function App() {
  const [value, setValue] = useState('');
  useEffect(() => {
    console.log(value);
  }, [value]);
  return (
    <div className='App'>
      <input
        type='text'
        onChange={(e) => {
          setValue(e.target.value);
        }}
      />
    </div>
  );
}

export default App;
```

![](https://velog.velcdn.com/images/qwzx16/post/72a1e1c4-de08-4a96-a88a-eb5f98787946/image.gif)

이정도는 사실 리엑트 배울때 처음 배우는 내용이라 모두가 알고있죠
우리는 (저는) useState에만 익숙해져 있는 상태로 오랫동안 공부를 해왔습니다..

# useRef

---

useRef에 대해 검색하면 많이 볼수 있는 단어가 있는데 그것은 상자라는 단어인데

```jsx
'use client';

import React from 'react';
import { scrollTo } from '../utils/scrollTo';
import Button from './Button';
import MainFullPage from '../pages/MainFullPage';
import MyInfo from './MyInfo';

const ScrollToExample = () => {
  const scrollRefs = [
    React.useRef<HTMLDivElement>(null),
    React.useRef<HTMLDivElement>(null),
    React.useRef<HTMLDivElement>(null),
    React.useRef<HTMLDivElement>(null),
    React.useRef<HTMLDivElement>(null),
  ];

  const handleClickScroll = (index: number) => {
    if (scrollRefs[index].current) {
      scrollTo(window, scrollRefs[index].current!.offsetTop);
    }
  };
  return (
    <div>
      <div className='fixed right-10 p-2 bg-orange-400 text-black h-screen z-10 flex flex-col gap-5 items-center justify-center'>
        <Button
          label='Scroll to 1'
          variant={'grey'}
          size={'md'}
          onClick={() => handleClickScroll(0)}
        ></Button>
        <Button
          label='2'
          variant={'grey'}
          size={'md'}
          onClick={() => handleClickScroll(1)}
        ></Button>
        <Button
          label='3'
          variant={'grey'}
          size={'md'}
          onClick={() => handleClickScroll(2)}
        ></Button>
        <Button
          label='Scroll to 4'
          variant={'grey'}
          size={'md'}
          onClick={() => handleClickScroll(3)}
        ></Button>
        <Button
          label='Scroll to 5'
          variant={'grey'}
          size={'md'}
          onClick={() => handleClickScroll(4)}
        ></Button>
      </div>
      <div className='flex flex-col gap-20'>
        <div className='w-screen h-screen transition-3s' ref={scrollRefs[0]}>
          <div>
            <div>안녕하세요</div>
            <div>저의 작업물입니다</div>
          </div>
        </div>
        <div className='h-screen w-screen transition-3s' ref={scrollRefs[1]}>
          Scroll to me 2!
        </div>

        <div className='h-screen w-screen transition-3s' ref={scrollRefs[2]}>
          Scroll to me 3!
        </div>
        <div className='h-screen w-screen transition-3s' ref={scrollRefs[3]}>
          ㅁㄴ안ㅁ어ㅏㄴㅁ로만
        </div>
        <div className='transition-3s' ref={scrollRefs[4]}>
          <MainFullPage children={<MyInfo />} />
        </div>
      </div>
    </div>
  );
};

export default ScrollToExample;

```

위 예시에서 div는 모두 각자 ref를 가지고 있고 그 ref.current를 콘솔로 출력해보면
![](https://velog.velcdn.com/images/qwzx16/post/ab82d060-abcb-4fb7-9a40-1d567dadb5b4/image.png)
div 태그 자체를 가지고 있다는 것을 확인할수 있습니다
div를 담을수 있는 상자라는 것입니다 !

![](https://velog.velcdn.com/images/qwzx16/post/dcf4f2d7-b99a-4744-9b12-c7f644a7174a/image.png)
간단한 예시로 위는 동일한 환경에서 ref.current.className을 출력해본 것입니다.
class를 이용해 스타일 변경에도 이용할수 있겠죠

## 버튼 예시

---

```jsx
import { useRef } from 'react';

export default function Counter() {
  let ref = useRef(0);

  function handleClick() {
    ref.current = ref.current + 1;
    alert('You clicked ' + ref.current + ' times!');
  }

  return (
    <button onClick={handleClick}>
      Click me!
      <p>{ref.current}</p>
    </button>
  );
}
```

이런 식으로 간단하게 값을 담을수도 있습니다.
![](https://velog.velcdn.com/images/qwzx16/post/8e1bef4d-e298-48fb-bba8-089298c5857b/image.gif)

위 예시를 보면 ref의 값은 매번 1씩 오르며 값은 분명히 변경되고 있지만
HTML이 렌더링이 되지 않기 때문에 보여지는 값은 계속 0 인것을 확인할수 있습니다.

## 인풋 예시

```jsx
import { useRef } from 'react';
import './App.css';

function App() {
  const inputRef = useRef(null);

  return (
    <div className='App'>
      <input type='text' ref={inputRef} />
      <button
        onClick={() => {
          console.log('ref : ', inputRef.current.value);
        }}
      >
        ref 보기
      </button>
    </div>
  );
}

export default App;
```

![](https://velog.velcdn.com/images/qwzx16/post/e30ae167-8050-4aef-87ce-1b65da3ec36d/image.gif)

이렇게 useRef는 참조하고 있는 정보(값 또는 HTML)를 이용하여 useRef로 선언한 값을 변경할수도 있고
그 값을 이용하여 무언가 할수 있습니다.
div를 참조하는 모습은 마치 VanilaJS의 querySeletor와 비슷해 보이기도 하네요

# 둘의 차이

useState와 useRef는 어떻게 본다면 비슷하다고도 볼수 있겠습니다.
초기값을 가지고 있고 그걸 변경할수도 있고 다양하게 이용할수 있으니까요
제가 생각하는 둘의 결정적 차이는 재렌더링을 시키냐 입니다.

setState를 이용해 값을 갱신하면 값이 변경됨과 동시에 렌더링이 일어나고 사용자는 그 변경된 값을 즉시 볼수 있게됩니다.

그리고 하나 더 차이가 있다면 ref는 div도 참조할수 있다는 것 정도인것 같으나 이번에 제가 적을 내용은 첫번째 차이와 더 큰 연관이 있습니다.

## 재랜더링에 관한 고찰

useState를 이용한 인풋과 useRef를 이용한 인풋의 차이는 위에서 보셨겠지만
useState를 이용하면 유저가 값을 입력할때마다 콘솔에 값이 출력됩니다.

그렇다면 인풋이 변할때마다 사용자가 보는 화면은 모두 재렌더링이 되는것이죠

반면에 ref를 이용한다면 불필요한 렌더링을 줄이고 필요한 값만 뽑아서 출력을 할수 있게 됩니다.

여기서 착각하기 쉬운게 재렌더링이 무조건 안좋냐 하면 그건 아닙니다 !

우리는 서비스를 이용하면서 검색버튼을 누르지 않아도 검색창에 입력한 정보가 전송되고 화면이 렌더링 되면서 그 검색 결과값을 화면에 보여주는 서비스를 이용해본적이 있을겁니다

![](https://velog.velcdn.com/images/qwzx16/post/6721a6cb-5dfe-46c5-a1a6-253310541ccf/image.gif)

~~마지막에 불편한 장면이 있긴 하지만 일단 넘어가죠..~~

이렇게 사용자의 입력한 값이 매번 필요한 경우도 있을겁니다.

그리고 아직 테스트를 해보지 않아서 모르겠지만 우리의 입력값을 인지하고 이전 검색기록을 보여주는것도 상태관리가 매번 되고 있기에 가능한건 아닐까요 ?

![](https://velog.velcdn.com/images/qwzx16/post/aee1ed61-4396-4e38-8ff7-93606bcbf133/image.gif)

이렇게 매번 렌더링 되는 화면은 사용자의 편의를 위해 필요할수도 있다 !

반면에 로그인이나 회원가입할때 사용되는 폼의 경우에는 사용자가 버튼을 눌러야만 입력값이 전송되도록 하여 불필요한 재렌더링을 줄이는 방법도 있겠죠

까지가 저의 생각이였습니다.

## 결론

리엑트에선 이것을 **Controlled Components(제어 컴포넌트)와 Uncontrolled Components(비제어 컴포넌트)**로 구분하고 있었습니다.

useState를 사용해서 만든 인풋은 제어 컴포넌트

- 리엑트가 제어하고 상태 관리를 한다

useRef를 사용해서 만든 인풋은 비제어 컴포넌트

- DOM에 접근하여 DOM을 조작해 참조만을 관리한다

라고 이해할수 있겠습니다.

리엑트 공식문서에 제어 컴포넌트와 비제어 컴포넌트에 대한 내용이 있는데

리엑트의 입장은 이렇습니다.

> 대부분 경우에 폼을 구현하는데 제어 컴포넌트를 사용하는 것이 좋습니다.
> 제어 컴포넌트에서 폼 데이터는 React 컴포넌트에서 다루어집니다.
> 대안인 비제어 컴포넌트는 DOM 자체에서 폼 데이터가 다루어집니다.

form의 인풋은 위에 저의 예시처럼 매번 입력을 받을때마다 그 값을 이용하여 이벤트를 하는 경우가 많습니다.

로그인이나 회원가입의 경우 비제어 컴포넌트를 사용해도 되지 않을까 ? 라는 고민도 매번 입력을 받아 유효성 검사를 해줄 필요가 있었죠

저의 이런 고민들은 공식입장 앞에서 무너졌습니다 ..
물론 비제어 컴포넌트를 사용해야할 일도 분명 있을것 입니다 !

class를 조작하여 애니메이션을 적용키는것 처럼 DOM 자체에 접근하여 조작해야할 경우가 있겠죠

정답은 없습니다
해당 페이지에 컴포넌트가 많아져 재렌더링의 비용이 비싸진다면 재 렌더링을 제어하기 위하여memo를 사용할수도 있겠고
만약 실시간 입력을 받아 보여줄 이벤트가 없다면 비제어컴포넌트를 사용하여 전송해주는 인풋을 만들수도 있겠죠

이렇게 사용자의 입장에서 고민하고 재렌더링의 여부가 클라이언트에 미치는 성능을 테스트하며 웹을 만들다보면 보다 더 각 컴포넌트의 활용도가 올라갈 것이라 생각합니다 !

마감일에 쫓기지 않는 선에서 충분히 고민하고 좋은 서비스를 만들기 위해 노력하겠습니다

긴 글 읽어주셔서 감사합니다 ..

틀린 내용에 대한 피드백은 언제나 수정하고 받아들이겠습니다 !

> **참고한 링크**

- https://velog.io/@hyunjine/useState-vs-useRef 비교글
- https://ko.legacy.reactjs.org/docs/hooks-reference.html#useref useState와 useRef 관련 공식문서
- https://react.dev/reference/react/useRef useRef 예시가 있는 공식문서
- https://legacy.reactjs.org/docs/uncontrolled-components.html 비제어 컴포넌트 관련 공식문서
- https://careerly.co.kr/qnas/2672 관련 내용 질의응답
- https://blog.toycrane.xyz/react-ref%EC%97%90-%EB%8C%80%ED%95%B4-%EC%9E%90%EC%84%B8%ED%9E%88-%EC%95%8C%EC%95%84%EB%B3%B4%EC%9E%90-f7d18d140716
