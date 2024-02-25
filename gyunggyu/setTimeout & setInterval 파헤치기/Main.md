# 개요

시간과 관련있는 두 Web API
간단한 설명을 먼저 해보자면

> setTimeout : 일정 시간 후에 원하는 함수를 실행해준다.
> setInterval : 일정 시간 간격을 두고 함수를 실행해준다.

딱 내가 알고 있는건 이 정도였고 이 API를 많이 쓰게 되리라고는 딱히 생각해본 적이 없다.

일단 기초가 탄탄해야 공사가 안정적인 법 기본 사용법 부터 알아봐야겠다.

## setTimeout 기본 문법

```javascript
const timer = setTimeout(functionRef, delay, param1, param2, /* …, */ paramN);
```

> functionRef : 실행하려는 함수를 넣어준다.
> delay : 기본값은 0 (ms)단위의 시간을 정수로 넣어준다. (ex: 1000 -> 1초)
> param1, param2 ...~ : 위에 실행하려는 함수에 함수에 파라미터를 전달해줄 경우 사용된다.

위 내용을 종합해서 setTimeout을 하나 사용해 보자면,

```javascript
const sayHi = (name) => {
  console.log(`${name[0]}하~`);
};

const timer = setTimeout(sayHi, 1000, (name = '경규'));
```

1초 후에 함수를 실행해 주고 파라미터를 전달해주었는데,

여기서 변수에 넣어 사용하는 이유는 사용자가 다른 이벤트를 실행하는 등 타이머가 불필요해지는 순간 `clearTimeout(timer)` 이런 식으로 취소해줄 수 있다.

여기서 delay는 기본 값은 0이며
**최소 보장 시간**을 제공한다.

## setInterval 기본 문법

```javascript
const interval = setTimeout(functionRef, delay, param1, param2, /* …, */ paramN);
```

호출하는 함수 명 빼고는 setTimeout과 동일하다

```javascript
const sayHi = (name) => {
  console.log(`${name[0]}하~`);
};

const interval = setInterval(sayHi, 1000, (name = '경규')); //1초마다 무한으로 경하가 콘솔에 찍힘

//불필요 할 시
clearInterval(interval);
```

## 둘의 차이?

간단하게 생각해서

setTimeout은 시간을 두고 한번 실행되고
setInterval은 시간 간격을 두고 무한히 실행된다.

setTimeout의 시간은 최소 보장 시간이다.

`clearTimeout`은 timer가 실행이 되지 않았으면 하는 순간에 실행해주면 timer가 제거된다.

예를 들자면 웹에 접근해서 10초 이상 아무런 동작이 없다면 '나가주세요' 라는 경고청을 띄워준다고 해보자면

사용자의 마우스 좌표를 체크하여 좌표가 움직일 시 clearTimeout() 메서드를 실행하여 경고를 띄울 필요가 없음을 지정해줄 수 있다.

`clearInterval`은 interval이 무한으로 실행이 되고 어떠한 조건에 따라 interval을 제거해준다.

내 프로젝트로 예시를 들어보자면 interval을 이용해서 베너 이미지를 10초마다 교체해준다.

그런데 베너가 화면에 없어지는 순간에 interval을 제거해 불필요한 동작을 막고 싶었고

```jsx
useEffect((
///interval 로직

  //컴포넌트가 언마우트 될 시
  return () => clearInterval(interval);
)=>[])
```

이렇게 처리 해주었다.

특정 페이지에서만 사용하는 timer도 위와 같이 clear를 해줘야 한다.

다른 페이지에 진입 시 불필요하게 동작할 수 있는 가능성을 막아줘야 하기 때문이다.

일단 기본적인 개념에 대해 이렇게 정리해 보았다.

여태 사용해왔듯이 다양하게 활용할 수 있을것 같다.

## 중첩 setTimeout

같은 동작을 하는 두 가지의 예시 코드를 긁어왔다.

### setInterval

```javascript
let i = 1;
setInterval(function () {
  console.log(i++, 'interval');
}, 100);
```

### setTimeout

```javascript
let i = 1;
setTimeout(function run() {
  console.log(i++, 'timer');
  setTimeout(run, 100);
}, 100);
```

setTimeout을 재귀적으로 호출하여 같은 동작을 하는 함수인데,

Interval로 하면 쉬운걸 왜 굳이 ?

여기서 setInterval과 setTimeout의 동작 방식에 대해 알아볼 필요가 있다.

![Alt text](<Interval 동작.png>)

Interval의 흐름을 극단적으로 보자면
이 함수는 동작 시간이 10초고 딜레이는 5초로 해두었다 쳐보자면

1번 함수 실행(10초 걸리는 함수) -> 2번 함수 실행 -> 3번 함수 실행 -> 1번 함수 실행 완료

이런 흐름이 된다.

물론 이런 극단적인 경우는 잘 없을지도 모르지만

딜레이가 너무 짧은 경우 | 함수의 로직이 복잡하거나 하는 이유로 실행 시간이 오래 걸리는 경우

Interval은 우리가 원하는 동작을 보장해주지 못할 수도 있다는 점을 명심해야겠다.

![Alt text](<timer 동작.png>)

반면에 setTimeout의 경우 명시한 delay는 보장된다.

함수가 종료 된 시점부터 delay만큼의 시간이 지연된다.

위와 같은 예시로 보자면

함수1 실행 -> 함수1 종료 -> 함수2 실행

보다 더 일관적인 동작을 해주기 때문에 필요한 순간이 있겠다.

## setTimeout의 최소 시간 보장

setTimeout의 dealy는 최소 시간 보장이라 하였다.

```javascript
const sayHi = (name) => {
  console.log(`${name[0]}하~`);
};

const timer = setTimeout(sayHi, 1000, (name = '경규'));
```

이 함수를 놓고 보자

아 설명을 빼먹었는데,

> timer로 선언해주는 것의 의미는 위에서 실행한 setTimeout에 id를 부여해 주는 행위다.
>
> 브라우저 내부에는 우리가 호출한 timer나 interval에 대해 id를 부여해준다.
> 그런데 우리가 그것을 알아차릴 방법은 없기 때문에
>
> 변수를 선언하듯이 이름을 짓고 오른쪽에 작성해주면 intervar이나 timer가 바로 호출 되면서 id가 부여되는 것이다.
>
> 이는 clear할때 사용할 수 있다.

아무튼 저렇게 우린 타이머 하나를 실행해주는데 delay는 1000ms의 시간을 무조건 보장해줄까?

정답은 아니다.

setTimeout은 Web API이자 비동기 작업이다.

setTimeout이 실행되면 위 함수는 브라우저의 API를 관리하는 공간인 Web APIs로 보내진다.

그 곳에서 delay만큼의 시간을 보낸다.

JS런타임에는 Call Stack과 비동기 작업에 대해 처리해주고 Queue로 동작하는 Event Loop가 있고,

setTimeout은 비동기 작업이기 때문에 Event Loop를 사용해 동작한다.

Event Loop는 Call Stack이 비어있을 시 실행된다.

setTimeout은 Web APIs에서 delay만큼의 시간을 보낸 후 Event Loop인 Task Queue로 보내진다.

그 후에 JS런타임에서의 Call Stack의 함수가 비워진 후에야 실행 순서가 된다.

**setTimeout의 생애**

호출 -> Web APIs(dealy 만큼 거처) -> JS런타임(Task Queue) -> Call Stack으로 이동시킴으로써 실행

그렇다면 최소 시간 보장이 되지 않는 setInterval은 어떻게 동작하는걸까 ?..

궁금하니까 알아보자.

다른 특징으로는

브라우저에서 동작하는 setTimeout과 서버에서 동작하는 setTimeout에 대한 내용이다.

대기시간을 명시하지 않으면 기본 값이 0인데,

브라우저에서는 다섯번째 중첩 타이머 이후에는 대기 시간을 최소 4ms로 지정해야한다는 제약이 있다고 한다.

그러나 서버 측에는 제약이 없다.

Node.js의 `process.nextTick`과 `setImmediate`를 이용하면 비동기 작업을 지연 없이 실행할 수 있다고 한다.

이런 경우까지 생각해보면 추 후에 어떤 문제가 생겼을때 하는 디버깅에 도움이 될 수 있겠다.

자 여기까지 setTimeout과 setInterval을 사용하면서도 생각 못했던 것들을 정리해 보았다..

사실 이렇게 이 둘을 정리하게 된 이유는 어떤 분의 합격 포트폴리오에 주식 관련 프로젝트가 있었고, 자동 로그아웃 되는 로직을 setTimeout을 이용해 구현하였더라던 것이였다.

복잡한 로직으로 구현한 것이 장점이 되었다고 해서 나도 뭔가 복잡한 무언가를 이것들로 구현할 수 없을까? 싶은 생각에 정리해보았다.

업무 관련해서 다양한 것들이 자동화가 되어가고 있고 사용자도 서비스를 사용할때 어떤 행위를 하지 않아도 정보를 얻고 싶을수 있다.

더 좋은 기능을 개발하기 위해 많은 것을 공부해야겠다.

~~그럼 20000..~~

**참고 링크**

> https://ko.javascript.info/settimeout-setinterval > https://velog.io/@conan/setTimeout-setInterval
