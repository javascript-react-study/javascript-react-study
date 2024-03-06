# 서론

모달이라 모달이라..
모달이라 함은 다양한 형식이 있죠.

뭔가를 작성하는 폼을 모달로 띄워줄수도 있겠고,
간단한 안내를 해주는 모달을 띄워줄수도 있겠고,
경고에 대한 모달을 띄워줄 수도 있겠는데,

제가 개인적으로 생각하는 모달의 용도는 2번 아니면 3번에 가깝습니다.

폼이 모달로 떠있으면 뭔가 모달이 해야할 역할 그 이상을 한다고 느껴져서 일까요?

그리고 제가 편하다고 느끼는 모달의 기능은 다음과 같습니다.

버튼은 많아야 두개정도 어떤 행위를 하는 버튼 한개와 그것을 닫아주는 버튼 하나

![](https://velog.velcdn.com/images/qwzx16/post/b28a9bf3-4112-4f60-9d9f-cfff76a8ae64/image.png)

이런 디자인?

그리고 기존 페이지와 모달을 확실히 분리시켜주기 위해 뒷 배경을 흐리게 해주거나 살짝 어둡게 해주는 것

또 ! 꼭 닫기 버튼을 누르지 않고 다른 곳을 클릭하더라도 모달이 닫히는 그게 바로 제가 생각하는 이상적인 모달이 되겠습니다.

![](https://velog.velcdn.com/images/qwzx16/post/3302c574-2c87-442b-ac4a-2aa8b83e88b5/image.png)

자 이런 느낌이 되겠습니다.

그래서 모달이 뭐 어쨌냐 하신다면..

모달을 만들어야 할 일이 잘 없다가 드디어 모달을 만들어야만 하는 일이 생겼어요.
알고리즘 문제를 푸는 사이트에 정답 유무를 알려주는 모달을 만들고 싶었죠.

버튼은 두개가 필요했어요.

- 이 페이지에 남아있기
- 문제 목록 사이트로 이동하기

이런 흐름을 원했고 또 한 모달이 번쩍! 하고 나타나는게 아니라 위에서 아래로 내려온다거나 하는 애니메이션도 넣고 싶었죠.

배경을 흐리게 해주거나 어둡게 해줄 것이기 때문에 그것도 차차 적용 되었으면 했어요.

이 정도의 생각을 하고 미루고 미뤄왔던 모달을 구현해 보았습니다.

보시죠

# 대충 설계?

뷰와 로직을 분리하기 위해서 그리고 제가 원하는 모달의 디자인을 만들기 위해 먼저 CSS를 입혀주는 작업을 먼저 했습니다.

그 후에는 useRef를 이용하여 배경과 모달을 각각 참조시켜줄 생각이였어요.

구현 같은 경우에는 일전에 학습했던 `IntersectionObserver`를 이용해 등장할때의 스타일을 입혀주면 되겠다 싶었습니다.

# 구현 과정

```jsx
if (isModal) {
  return (
    <div className='w-screen h-screen flex justify-center absolute z-50 transition-all'>
      <div ref={backgroundRef} className='w-full h-full absolute transition-1s ' />
      <div
        ref={modalRef}
        className={cn(
          darkModeClasses,
          `absolute border-main-color p-8 rounded-md flex items-center justify-center flex-col gap-5 shadow-lg transition-all -translate-y-60 bg-white`,
        )}
      >
        <p className='text-xl p-5 text-blue-400'>나가주세요</p>

        <div className='flex gap-3'>
          <Button label='닫기' bg={'alert'} onClick={() => setIsModal(false)} />
          <Button label='나가기' bg={'main'} />
        </div>
      </div>
    </div>
  );
}
```

정답 유무에 따른 안내를 해주기 위한 분기를 하나 파줬고
두개의 버튼 색은 다르게 해주었습니다.

![](https://velog.velcdn.com/images/qwzx16/post/53f72303-65f3-44c1-9de5-9b32553c9d2a/image.png)

이런 식으로 모양이 만들어졌네요 !

이제 ref와 `IntersectionObserver`를 이용하여 모달이 나타날때 스윽 하고 나타날 수 있도록 해주겠습니다.

```jsx
const backgroundRef = React.useRef(null);
const modalRef = React.useRef(null);
```

일단 두개의 DOM을 참조하는 각각의 Ref를 만들어줍니다.

이제 저 ref 바라봐주는 observer를 만들어줄게요

```jsx
const modalObserver = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      if (entry.target) {
        entry.target.classList.add('translate-y-10');
      }
    } else {
      if (entry.target) {
        entry.target.classList.remove('-translate-y-60');
      }
    }
  });
});
const backgroundObserver = new IntersectionObserver((entries) => {
  entries.forEach((entry) => {
    if (entry.isIntersecting) {
      if (entry.target) {
        entry.target.classList.add('bg-black');
      }
    } else {
      entry.target.classList.remove('bg-black');
    }
  });
});
```

observer를 만들어 주었고 저는 모달은 위에서 아래로 내려오기 바라고
배경은 흑색이 되길 바랍니다.

opacity의 경우에는 그냥 배경 스타일에 지정해주었어요.

modal에는 translate를 이용해 애니메이션을 주었고,
background는 배경색을 이용해 애니메이션을 주었습니다.

단순히 스타일을 추가했다 빼줬다 해주고 transition을 지정해주어서 애니메이션을 넣어준 것이죠 !

이젠 만들어둔 각 observer가 ref를 바라볼 수 있도록 해주겠습니다.

### 부착

```jsx
if (modalRef.current) {
  modalObserver.observe(modalRef.current);
}

if (backgroundRef.current) {
  backgroundObserver.observe(backgroundRef.current);
}
```

### 탈착

```jsx
if (modalRef.current) {
  modalObserver.unobserve(modalRef.current);
}
if (backgroundRef.current) {
  backgroundObserver.unobserve(backgroundRef.current);
}
```

이런식으로 구현해주면 modal과 background 요소를 참조하는 ref를 observer가 바라보게 됩니다.
이 요소가 보일때, 안보일때 저희가 원하는 class를 탈부착 하는 동작을 하게 됩니다.

이제 이 요소들을 바닥에 냅다 뿌리는 것이 아니라,
isModal의 여부에 따라 옵저버가 탈부착 되기를 원합니다.

isModal이 true가 되면 DOM요소에 observer를 부착 해줌과 동시에 저희가 선언해 놓은 기능을 수행할 수 있도록 해주고 false가 된다면 observer를 탈착하여 쓸데 없이 observer가 남아있지 않도록 해줄 예정입니다 !

isModal에 따라 동작하는 useEffect로 구현을 해보겠습니다 !

```jsx
React.useEffect(() => {
  const modalObserver = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        if (entry.target) {
          entry.target.classList.add('translate-y-10');
        }
      } else {
        if (entry.target) {
          entry.target.classList.remove('-translate-y-60');
        }
      }
    });
  });
  const backgroundObserver = new IntersectionObserver((entries) => {
    entries.forEach((entry) => {
      if (entry.isIntersecting) {
        if (entry.target) {
          entry.target.classList.add('bg-black');
        }
      } else {
        entry.target.classList.remove('bg-black');
      }
    });
  });

  if (modalRef.current) {
    modalObserver.observe(modalRef.current);
  }

  if (backgroundRef.current) {
    backgroundObserver.observe(backgroundRef.current);
  }

  return () => {
    if (modalRef.current) {
      modalObserver.unobserve(modalRef.current);
    }
    if (backgroundRef.current) {
      backgroundObserver.unobserve(backgroundRef.current);
    }
  };
}, [isModal]);
```

자 이렇게 해주면 이제 isModal에 따라 observer가 선언 됨과 동시에 true일 경우 부착 false일 경우 탈착해주는 기능이 구현된 것 같습니다..

테스트를 위해 모달을 띄워주는 버튼을 만들었고,

또 한 배경을 클릭해도 모달이 닫히도록 기능을 부여해 주었습니다.

그리고 각 버튼에 기능을 부여해 주었습니다..

```jsx
            <Button label='닫기' bg={'alert'} onClick={() => setIsModal(false)} />
            <Button
              label='나가기'
              bg={'main'}
              onClick={() => (window.location.href = 'https://www.naver.com')}
            />
```

예 나가기를 누르면 네이버로 이동할 것입니다.

이 버튼의 기능도 용도에 맞게 사용하면 될 것이고 단순히 안내만 해주는 경우에는 닫기 버튼만 있어도 무방해보이죠.

그럼 이제 동작이 잘 되는지 확인해보겠습니다 !

![](https://velog.velcdn.com/images/qwzx16/post/2e0df24a-1e66-4cf3-84e0-32acc0f8ede3/image.gif)

예 잘나가집니다..

# 남은 과제

- useEffect와 useState
- jsx

이 두개를 나누어 스타일 관련 코드와 로직 관련 코드를 분리 시켜준다면 잘 써먹을 수 있을 것 같습니다.

요즘 `IntersectionObserver`를 이용하여 다양한 기능을 구현해보고 있는데,
이제 무한 스크롤 정도만 해본다면은 이걸로 할 수 있는건 거진 다 해본거지 싶습니다.

아무쪼록 제가 모달을 설계하고 그걸 하나하나 구현하는 과정을 보았는데 유용했으면 하네요.

사실 모달이 아니여도 다양하게 사용할 수 있을 것 같긴 합니다 ! 그럼 감사합니다 (꾸벅)
