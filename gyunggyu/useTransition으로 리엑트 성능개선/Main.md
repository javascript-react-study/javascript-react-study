# 리엑트 성능개선

## 서론

다양한 프로젝트를 하며 비동기 통신과 이미지 렌더링 중에 뭔가 느리게 렌더링 되는 컴포넌트를 많이 보았어요.
생각만 하고 해결책에 대해 찾아보다가 이번에 테스트를 하며 적용해 봤습니다.
적용 방법도 간단하고 유용한 정보가 될것 입니다 !

## 필요한 이유

- 이미지 렌더링, 파일 렌더링
- 비동기 처리
- 너무 많은 컴포넌트의 re-render

이러한 작업의 경우 컴포넌트 렌더가 지연되는 경우가 생깁니다.
성능 관련 이슈를 처리해 보아도 안되는 경우가 분명 있을겁니다.
그런 경우에는 약간의 눈속임과 코드 실행시점을 조정해주는 `useTransition`을 사용할수 있는데,
그 사용법에 대해서 알아보죠 !

**여기서 중요한건 실제로 성능개선을 해주는 것이 아니라 코드의 동작 순서를 조작하여 성능개선이된 것처럼 보이게 한다는 것입니다!
편의상 성능개선이라고 하겠습니다.**

## useTransition 사용해보기

---

### 성능이슈 발생

아래의 코드를 보시죠.
배열을 무려 오만개를 만들어서 인풋이 작성될때 마다 보여주게 만들었습니다..
이런 경우에는 너무 많은 컴포넌트가 재렌더링 되는 탓에 성능 이슈가 생기게 되었고,
저의 맥북은 견디지 못하고 성능 저하가 되었죠..

```jsx
import React from 'react';
import './App.css';

const App = () => {
  const arr = Array.from({ length: 50000 }, () => 0);
  const [value, setValue] = React.useState('');
  const [isPending, startTeansition] = React.useTransition();
  return (
    <div className='w-screen h-screen flex flex-col'>
      <div>
        <input
          onChange={(e) => {
            setValue(e.target.value);
          }}
          className='border'
          type='text'
        />
        {arr.map(() => {
          return <div>{value}</div>;
        })}
      </div>
    </div>
  );
};

export default App;
```

![![![Alt text](Input%EC%84%B1%EB%8A%A5%EC%9D%B4%EC%8A%88.gif)](<Input 성능개선 이전.gif>)](<Input 성능개선 이전.gif>)

보시는것과 같이 타이핑을 하고 지연이 된 후에 인풋에 리렌더가 됩니다.
~~아주 답답하죠~~
그렇다면 `useTransition` 훅을 이용하여 성능을 개선해볼까요 !

---

### 1차 성능개선

이제 `useTransition`을 사용해봅시다 !

#### 동작원리

일단 `useTransition`의 동작 원리를 간단하게 보자면
컴포넌트가 렌더링 되는것은 그 값이 변할때 인데 보통 값이 변하는 것은 함수로 인해 변하죠,
해당 함수를 지정해 주고 그 함수로 변경되는 컴포넌트를 먼저 렌더링 처리해 주는 것입니다 !
간단히 생각해 보자면 우선순위를 바꿔서 성능이 향상되는것 처럼 보일수 있게 하는것입니다.

#### useTransition의 구성요소

`useTransition`은 배열의 형식으로 0번째 인덱스에는 `boolean`형식의 값이 있고 1번째 인덱스에는 함수가 하나 있습니다.

![Alt text](<useTransition Console.png>)

#### 사용방법

`const [isPending, startTransition] = useTransition()`
이런식으로 꺼내서 사용하시면 되는데 1차적으로는 동작에 영향을 주는 `startTransition`을 사용해서 성능개선을 해보겠습니다.

```jsx
import React from 'react';
import './App.css';

const App = () => {
  const arr = Array.from({ length: 50000 }, () => 0);
  const [value, setValue] = React.useState('');
  const [isPending, startTeansition] = React.useTransition();
  return (
    <div className='w-screen flex flex-col items-center'>
      <p className='text-lg'>테스트 인풋</p>
      <input
        onChange={(e) => {
          startTeansition(() => {
            setValue(e.target.value);
          });
        }}
        className='border'
        type='text'
      />
      {arr.map(() => {
        return <div>{value}</div>;
      })}
    </div>
  );
};

export default App;
```

위 코드를 변경 전의 코드와 비교해보면 `startTransition`함수를 이용해서 우리가 먼저 렌더링 되었으면 하는 함수의 렌더링을 담당하는
인풋의 `setValue`함수를 감싸주었죠 ?
이렇게 했을때 우리의 예상대로라면 `input`의 변경 시점은 오만개의 `div`박스가 렌더되는것 보다 우선적으로 변경되어야 할것 입니다.

![Alt text](<Input성능개선 1차.gif>)

1. 인풋을 먼저 렌더링 해준다
2. 오만개의 밸류를 렌더링 해준다

이런 순서로 작업을 처리하기 때문에 사용자의 입장에선 타이핑이 막히지 않아 지연이 되지 않는것 처럼 보이게 해주는 것입니다!

이것만으로도 눈에 띄는 성능 개선이 되지만 `useTransition`은 놀라운 기능을 하나 더 제공합니다 !

---

### 2차 성능개선

아까 0번째 인덱스에 있던 `isPending`이라는 `boolean`값을 기억하시나요 ?
이름에서 알수 있듯 렌더링이 렌더링이 진행중인 컴포넌트가 있을 경우 `true`를 반환해주고,
없을 경우에는 `false`를 반환해 주는 값이였습니다.

이것으로 우리는 오만개의 밸류가 완전히 렌더링이 되기 전에는 로딩중이라는 표시를 해줄수 있을거란걸 금방 생각하실수 있을겁니다 !

```jsx
import React from 'react';
import './App.css';

const App = () => {
  const arr = Array.from({ length: 50000 }, () => 0);
  const [value, setValue] = React.useState('');
  const [isPending, startTeansition] = React.useTransition();
  return (
    <div className='w-screen flex flex-col items-center'>
      <p className='text-lg'>테스트 인풋</p>
      <input
        onChange={(e) => {
          startTeansition(() => {
            setValue(e.target.value);
          });
        }}
        className='border'
        type='text'
      />
      {isPending ? (
        <div>Loading..</div>
      ) : (
        arr.map((el, i) => {
          return <div key={i}>{value}</div>;
        })
      )}
    </div>
  );
};

export default App;
```

삼항연산자를 이용하여 `isPendiong`이 `true`일 경우 보여줄 요소를 만들었습니다.

![Alt text](<Input 성능개선 2차.gif>)

처음과 비교하여 놀라운 성능개선을 이뤄냈습니다 !
성능개선은 여기까지 해보겠습니다..

## useTransition이 유용한 이유

성능개선에 대한 해결책은 여러가지가 있을수 있습니다.
애초에 컴포넌트 즉 `HTML`요소 자체를 줄이는 방법도 있을수 있고,
`ReactQuery`에서도 요청에 대한 `Pending`처리를 지원해주기 때문에 요청에 대해서도 매끄럽게 처리할수 있죠,

하지만 위와 같이 Input이 버벅거리는 경우와 어떤 요소가 우선 렌더링 되어야 하겠다 싶은 경우에는 유용하게 사용할수 있겠다는 생각이 들었습니다.
새로운 서비스를 만드는 경우도 많지만 만들어져있는 서비스를 리펙토링할 일이 더 많아질 것이기 때문에 임시방편으로 사용하기에 간단하고
사용자의 경험을 손쉽게 상승시킬수 있다는 점이 알아두면 쓸모있겠다는 생각이 들었습니다!
