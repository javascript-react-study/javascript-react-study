# 리엑트의 사이드 이펙트에 대해 아시나요 ?

## 프로그래밍에서의 사이드 이펙트(Side Effect)란?

코딩 표준 문서에서는 사이드 이펙트에 대해 이렇게 설명해요.

- 빈 구문이 아니라면 하나의 Side Effect를 가져야 한다.
- Side Effect가 일어나지 않는 구문은 프로그래밍 에러를 발생한다.

즉 Side Effect는 실행 도중 어떠한 객체를 접근해서 변화를 일으키는걸 의미하고,

이건 함수 하나가 한가지의 변화만 일으키는 순수 함수를 의미합니다.

꼭 함수가 아니더라도

```
let name = '강경규'
```

변수 선언으로 인한 사이드 이펙트 발생

```
name = '또띠'
```

변수 재할당으로 인한 사이드 이펙트 발생

이렇게 두번의 사이드 이펙트가 발생했습니다.

이런 사이드 이펙트가 일어나는 것 자체는 당연하지만 전역변수를 남발하거나 함수 하나에 너무 많은 사이드 이펙트를 발생시키게 된다면 디버깅의 과정이 까다로워 진다는 것을 우리는 알수 있습니다.

사이드 이펙트가 하나만 있는 함수의 장점은 코드 디버깅을 하기 편해지고 해당 함수에 대한 리펙토링 작업도 수월해질 것입니다.

이렇게 프로그래밍에서 사이드 이펙트가 어떤 의미인지 간단히 짚어봤어요.

그래서 뭐 그게 어쨌냐면은..

## 리엑트에서의 사이드 이펙트에 대해

리엑트에서 주로 일어나는 사이드 이펙트의 종류는

- API 호출
- 타이머
- DOM 조작

이런 비동기 작업이 있는데,

구글에 검색해보면 다양한 글에서 리엑트에서 사이드 이펙트를 어떤 방식으로 처리하냐는 말에 useEffect를 사용하라고 다들 확답을 내놓고 있습니다.

그런데 요즘 useEffect에 대한 이야기도 많이 보이고 저도 호기심이 생겨서 알아봤는데

과연 리엑트에서 사이드 이펙트를 처리할땐 useEffect가 정답일까요 ?

리엑트에서 권장하는 사용법 그리고 올바르게 사용하는 방법에 대해 찾아본 내용을 한번 정리해보겠습니다.

### 리엑트 컴포넌트의 순수성

`순수 함수`에 대해서 아십니까?

동일 입력에 대해 동일 출력을 해야하고 자신의 일에 대해서만 신경을 써야하는 함수에 대한 정의입니다.

리엑트 컴포넌트에도 동일하게 적용되는 내용입니다.

**순수 컴포넌트**

```jsx
import './App.css';

function App() {
  return (
    <>
      <Button label='버튼이름' />
    </>
  );
}

export default App;

export const Button = ({ label }) => {
  return <button>{label}</button>;
};
```

위 버튼은 순수 컴포넌트의 예시라고 볼수 있습니다.

버튼 이름을 넣으면 넣은 그대로 버튼을 보여줍니다.

![Alt text](<버튼 컴포넌트.png>)

**순수하지 않은 컴포넌트**

```jsx
import './App.css';
let number = 0;
function App() {
  return (
    <>
      <안순수함 />
      <안순수함 />
      <안순수함 />
    </>
  );
}

export default App;

export const 안순수함 = () => {
  number = number + 1;
  return <div>안순수함{number}</div>;
};
```

![Alt text](<순수하지 않은 컴포넌트.png>)

컴포넌트를 똑같이 쓰는데 매번 다른 결과를 보여줍니다.

이렇게 되면 로직이 복잡해질 시 동작을 예측할수 없고 디버깅을 할수 없는 순수함을 지키지 못한 컴포넌트가 되는 것입니다.

**그런데 !**

우리가 만드는 페이지는 정적인 페이지만 만들지 않죠?

정적인 렌딩 페이지를 만드는 경우도 물론 있겠지만 다양한 이벤트를 통해 언젠가 어디선가 무언가 바뀌어야 한다는 것은 모두가 알고 있습니다.

그리고 저희는 사이드 이펙트를 많이 쓰고 다양한 동작에 대해 남발하고 있습니다.
이는 대부분 클릭 스크롤 등의 이벤트 핸들러에서 발생합니다.

~~일단 저의 생각으로는 컴포넌트의 순수성을 모두 지킨다는건 말이 안된다고 생각합니다.~~

### 리엑트가 Effect를 정의하다.

위에서 말한 이벤트 핸들러로는 우리가 필요한 동작을 모두 해줄순 없습니다.

그래서 리엑트는 Effect에 대해 정의하는데 이것은 렌더링으로 발생하는 사이드 이펙트를 정의한 것입니다.

이게 바로 렌더링으로 발생하는 사이드 이펙트를 처리해주는 useEffect 훅을 의미하는게 됩니다.

~~useEffect를 사용하는 방법은 스킵해보겠습니다.~~

그리고 저는 그런 사이드 이펙트 중에 하나인 API요청으로 하는 Data fetch에 대한 것을 useEffect로 처리한 경험이 실제로 있습니다.

> 웹페이지 접근 -> 리뷰에 대한 정보 바로 불러오기 && 로그인 유무 확인

```jsx
import './App.css';

import { Routes, Route } from 'react-router-dom';
import { useEffect } from 'react';

import Header from './components/Header';
import Join from './pages/Join';
import Login from './pages/Login';
import MyPage from './pages/MyPage';
import Serch from './pages/Serch';
import Home from './pages/Home';
import Detail from './pages/Detail';
import Create from './pages/Create';
import DetailReview from './pages/DetailReview';
import DarkModeButton from './components/DarkModeButton';
import KakaoLogin from './pages/KakaoLogin';
import useDarkMode from './hooks/useDarkMode';
import useLogin from './hooks/useLogin';
import useContentFetch from './hooks/useContentFetch';

function App() {
  const { darkMode, toggleDarkMode } = useDarkMode();
  const { fetchLogin } = useLogin();
  const { fetchReview } = useContentFetch();

  useEffect(() => {
    fetchLogin();
    fetchReview();
  }, []);

  return (
    <div className={`${darkMode ? 'bg-zinc-800 text-white' : ''} transition-all`}>
      <Header />
      <div className='mt-12'>
        <Routes>
          <Route path='/' element={<Home />} />
          <Route path='/mypage/:userId' element={<MyPage />} />
          <Route path='/join' element={<Join />} />
          <Route path='/login' element={<Login />} />
          <Route path='/serch' element={<Serch />} />
          <Route path='/detail/:mediaType/:contentId' element={<Detail />} />
          <Route path='/detail/review/:userId/:reviewId' element={<DetailReview />} />
          <Route path='/create/:mediaType/:contentId' element={<Create />} />
          <Route path='/auth' element={<KakaoLogin />} />
        </Routes>
      </div>
      <DarkModeButton darkMode={darkMode} toggleDarkMode={toggleDarkMode} />
    </div>
  );
}

export default App;
```

166번째줄의 useEffect 사용하는 부분을 보시면 아실수 있죠.

그런데 리엑트가 정의하는 Effect는 그렇게 사용되어서는 안된다고 합니다 ?

![Alt text](%EC%9D%B4%EC%99%9C%EC%A7%84.png)

~~나만 그렇게 썻나요?~~

리엑트가 정의하는 `Effect`

- 서버에서 실행되지 않는다.
- 직접 fetch(axios)를 하면 **네트워크 워터폴**이 만들어 지기 쉽다.
- 직접 fetch(axios)를 하는 것은 **데이터를 미리 로드하거나 캐시하지 않는다**는 것을 의미한다.

그니까 여러개의 요청이 쌓이면서 네트워크 워터폴 현상이 발생하고 캐싱이 되지 않는다는 문제점을 지적한 것이죠.

https://velog.io/@han-byul-yang/React-React-data-fetchingwaterfall-%ED%98%84%EC%83%81

그래서 리엑트는 fetch를 useEffect를 사용할 것이 아닌 다른 방법을 재시합니다.

- 직접 만들어라.
- Next.js와 같은 fetch 메커니즘을 사용해라.
- React Query, useSWR 등의 오픈 소스를 활용해라.

데이터 패치에 대해서는 이펙트를 사용하지 말라는 리엑트의 `엄중경고` 였구요.

### 그럼 어떻게 사용하죠

첫째로 저희가 생각해봐야 하는것은

- 핸들러 이벤트로 인해 발생하는 이펙트인가?
- 렌더링으로 인해 발생하는 이펙트인가?

이 둘을 구분할줄 아는 것입니다.

두번째로 렌더링을 위한 데이터 변환인가? 를 생각해 봐야 합니다.

> 예를 들어 State를 바꿔줌으로써 강제로 렌더링을 시키는 로직은 좋지 않은 로직이 됩니다.

세번째로 **렌더링 중에 해결할수 있는가?** 를 생각해 봐야 합니다.

~~어렵습니다~~

### 불필요한 useEffect를 줄여보자

#### 성과 이름을 합쳐 풀네임을 얻는 과정입니다.

❌

```jsx
export const Form = () => {
  const [성, set성] = React.useState('강');
  const [이름, set이름] = React.useState('경규');
  const [성이름, set성이름] = React.useState('');

  React.useEfect(() => {
    set성이름(성 + 이름);
  }, [성, 이름]);
  // ...
};
```

두개의 State를 의존성 배열에 주어 변경될때마다 State를 변경해주는 이런 구조는 좋지 않습니다.

✅

```jsx
export const Form = () => {
  const [성, set성] = React.useState('강');
  const [이름, set이름] = React.useState('경규');
  const 성이름 = 성 + 이름;

  // ...
};
```

이렇게 하나의 변수로만 해주어도 우린 같은 동작을 하는 코드를 만들수 있겠죠 풀네임을 얻을수 있게 됩니다.

#### 프로필에 코멘트를 남길수 있는데 userId가 변할때 코멘트 State를 초기값으로 만들어 주는 과정입니다.

❌

```jsx
export const ProfilePage = ({ userId }) => {
  const [comment, setComment] = React.useState('');

  React.useEfect(() => {
    setComment('');
  }, [userId]);

  //...
};
```

userId를 prop으로 받고있는 컴포넌트 입니다.
useId가 변경될때 마다 comment State를 초기값으로 재설정 해주어야 하는 상황이죠.
✅

```jsx
export const ProfilePage = ({ userId }) => {
  return <Profile userId={userId} key={userId} />;
  //...
};

export const Profile = ({ userId }) => {
  const [comment, setComment] = React.useState('');
  // ...
};
```

이럴때도 useEffect에 의존성 배열을 이용하기 보다는 Profile이라는 컴포넌트를 새로 만들어주고 userId를 Key로 넘겨준 후에 Key값이 변경될때마다 렌더링 되는 React의 특성을 이용할수 있겠죠.

렌더링이 되면 comment는 자동으로 초기값이 될테니까 말이죠!

#### 재렌더링을 원하지 않고 일부 State만 바꾸고 싶은 경우입니다.

❌

```jsx
export const List = ({ items }) => {
  const [isReverse, setIsReverse] = React.useState(false);
  const [selection, setSelection] = React.useState(null);
  useEffect(() => {
    setSelection(null);
  }, [items]);
  // ...
};
```

이것도 마찬가지로 items라는 prop이 변경될때 마다 selection이라는 상태를 초기로 재설정 해주고 싶습니다.

그런데 이번엔 isReverse라는 상태가 추가되었기 때문에 seletion만 재설정 시켜주고 싶기 때문에 위 방법은 사용하기 어렵습니다.

✅

```jsx
export const List = ({ items }) => {
  const [isReverse, setIsReverse] = React.useState(false);
  const [selection, setSelection] = React.useState(null);
  const [isPrevItems, setIsPrevItems] = React.useState(items);

  if (items !== prevItems) {
    setIsPrevItems(items);
    setSelection(null);
  }
  // ...
};
```

그런 경우에도 이전 items 담아두는 상태를 하나 만들어서 새로운 prop을 받아서 이전 아이템과 받아온 아이템이 다를 경우에 prevItems의 상태와 seletion의 상태를 변경해 주는 조건문을 만들어서 **렌더링 중에** 실행될 수 있도록 해줍니다.

**코드가 이해하기 어려운뎁쇼**

맞습니다.
다른 대안이 있는데 이 대안에는 items가 독립적인 id를 가진 경우에 작성할수 있는 코드입니다.
그런데 저희가 데이터를 다룰땐 보통 독립적인 id가 있단 말이죠.

```jsx
export const List = ({ items }) => {
  const [isReverse, setIsReverse] = React.useState(false);
  const [selectionId, setSelectionId] = React.useState(null);

  const selection = items.find((item) => item.id === seletionId) ?? null;
  // ...
};
```

이렇게 seletion을 변수로 처리해주고 선택한 아이템이 있다면 seletion을 반환해주고 그게 아니라면 null을 반환해 주는 방식도 사용할수 있겠습니다.

Javascript가 제공하는 find메서드로도 동일한 환경이 구현이 가능하다는 것을 알수 있었습니다.

## 결론

리엑트에서 이야기하는 Effect에 대한 내용은 정말 많습니다.
반응형-비반응형 데이터에 대한 처리, 다양한 기능의 Effect Hooks`(useLayoutEffect, useInsertionEffect)` 등등 ..
저는 오늘 리엑트 컴포넌트의 순수성부터 useEffect가 필요해진 이유, 불필요한 useEffect를 리펙토링 하는 법에 대해 공부해봤습니다.

서버에서 주는 데이터는 React-Query와 같은 라이브러리로 관리하거나 직접 fetch를 만드는 부분에 대해서는 리펙토링이 들어가야겠고 남발했던 useEffect에 대해 돌아볼수 있었네요.

아무쪼록 더 좋은 코드를 짜기 위해선 단순 기능 구현이 아닌 효율적인 로직에 대해 잘알아야하고 그 기반에는 탄탄한 Javascript 실력이 필요하겠구나 그리고 제공받는 라이브러리들의 기능을 용도에 맞게 쓰는 능력도 중요하다는 것을 알수 있었습니다..

~~그럼 2000.. 즐코딩이용~~~
