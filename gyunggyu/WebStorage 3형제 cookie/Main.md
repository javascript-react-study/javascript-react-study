# WebStorage란 ?

웹스토리지는 클라이언트 측에서 데이터를 지속적으로 저장하고 사용할 수 있게 합니다.

간단하게 제가 해석해보자면

> 웹에서 제공하는 가벼운 데이터베이스

정도로 볼 수 있겠습니다.

웹스토리지에는 세가지 종류가 있어요.

- cookie
- LocalStorage
- SessionStorage

그래서 오늘은 이 셋에 대해 간단히 알아보고 이 셋을 잘 활용하는 방법을 알아볼게요!

## 셋에 대해 아라보자

### cookie

쿠키는 뭐랄까 이 셋중에서 가장 복잡한 웹스토리지입니다.
특징을 먼저 나열해 보자면

- 키, 값 형태로 문자열을 저장할수 있다.
- 만료시간, 상태 유지를 위한 다양한 설정을 할수 있다.
- 서버와 클라이언트 간의 데이터 교환을 하는데에 쓰일수 있다.

> 서버에 필요한 간단한 문자열을 유효기간을 정해두고 저장할 때 사용할 수 있겠습니다.

> 물론 서버에 필요 없어도 굳이 로컬,세션 스토리지에 저장하지 않고 간단한 문자열을 저장하고 싶다면 사용해도 무방하겠습니다.

### LocalStorage & SessionStorage

**공통**

- 키 값 형태로 JS 원시형 타입을 저장할 수 있다.
- 5~10MB의 용량을 가져서 쿠키보다 훨씬 많은 데이터를 보관할 수 있다.
- 클라이언트 측에서 저장되는 데이터로 서버로 데이터가 전송되지 않는다.
- 도메인당 1개의 저장소가 생성된다. 즉 다른 도메인 서브도메인 까지도 데이터가 공유 되지 않는다.

**로컬 스토리지**

- 해당 도메인에 접근하는 기기에 영구적으로 데이터가 보관 된다.

**세션 스토리지**

- 브라우저가 종료 되면 데이터가 사라진다.

이 둘의 차이는 데이터가 얼마나 지속되냐에 있고 나머지는 동일하다고 볼 수 있겠습니다 !

> 나의 클라이언트 안에 나만의 작은 DB를 만든다고 생각하면 좋겠습니다.

예전에는 이런 웹스토리지로도 간단한 프로젝트를 만들어 취업하고 했다지만 요즘엔 택도 없어보이죠?

사실 용도의 경우 위 특징에 알맞는 곳에 사용하면 됩니다.

저의 경우 로컬 스토리지는 데이터 영구성의 강점이 있기 때문에 다크모드를 주로 구현했고,

클라이언트 측에 로그인 세션 식별자를 저장하고 요청 시 서버로 쉽게 보내기 위해 쿠키를 사용했었습니다.

세션스토리지는 뭐 .. 안써봤어요 !
그런데 생각하기로는 비회원 장바구니 그런거에 사용할 수 있을 것 같습니다.

## 사용법

### cookie

쿠키의 경우 저는 좀 써본 기억이 있는데,
두가지의 방법을 소개해보겠습니다 !

#### session & cookie

**주의 : session은 세션스토리지와 다릅니다.**
로그인 기능을 구현할 때 발급받은 session 식별자를 쿠키에 전달하여 인증/인가를 하는 작업을 했었습니다.

위에서 말씀드린대로 쿠키는 서버와 통신을 할 수 있기 때문에 그런 특징을 이용해서 인증/인가를 손쉽게 구현할수 있었고,

그를 간단히 제공하는 라이브러리인 passport를 사용했어요.

일단 흐름을 한번 살펴보자면

![](https://velog.velcdn.com/images/qwzx16/post/fe1cea56-62c5-4b79-968a-42c37446675f/image.png)

![](https://velog.velcdn.com/images/qwzx16/post/a5567ede-40cc-47a9-9413-81b4d87588a3/image.png)

이런 형식인데 핵심은 로그인 시 서버에서는 이메일과 비밀번호가 맞는지 검증하는 작업이 이뤄지고 그것에 성공하면 passport는 session 식별자를 생성해주고, 그것을 클라이언트 측에 쿠키로 저장하게 됩니다.

이 session 식별자는 유저가 새로고침 혹은 인증/인가가 필요한 작업을 할때마다 쿠키를 이용하여 로그인 여부를 체크할 수 있게 됩니다.

코드의 경우 passport 라이브러리가 제공하는 코드에서 이것을 알아서 컨트롤해주기 때문에 여기서는 session & cookie를 이용한 로그인의 흐름을 알 수 있었습니다.

![](https://velog.velcdn.com/images/qwzx16/post/0cfa38fc-76e2-4181-a357-174ddc6239fb/image.png)

로그인을 하면 `connect.sid`라는 쿠키가 생성 됩니다.

![](https://velog.velcdn.com/images/qwzx16/post/ae879ec4-a1d4-4aa2-954d-436d5e3d12ef/image.png)

로그아웃을 하면 없어집니다.

그러나 HTTP배포 환경에서는 이게 제대로 작동하지 않습니다.
보안 문제 때문인데 HTTPS환경, 로컬환경에서만 이런 방식이 제대로 작동한다는 점을 유의하십쇼..

#### 클라이언트 측에서 쿠키 관리법

최근에 구현한 내용입니다.

일단 문제는 이렇습니다.

팀원들과 개발하는 프로젝트에서 발생한 이슈인데,
저희 프로젝트에는 두개의 서버가 있었습니다.

그 둘은 메인도메인 - 서버도메인으로 이루어져 있었고,
기존에 JWT 토큰을 로컬스토리지에 저장하여 사용하였습니다.

ngrok을 이용한 개발 환경에선 로컬스토리지가 서브도메인으로 공유가 잘되었으나,
배포 이후에는 공유가 되지 않는 것입니다.

**메인 도메인의 로컬 스토리지**
![](https://velog.velcdn.com/images/qwzx16/post/4cd3c460-4993-469f-a28f-125529695cb7/image.png)

간략하게 말씀 드리자면,

> 메인 도메인 클라이언트의 로컬스토리지에서 관리하던 JWT 토큰이 서브 도메인의 클라이언트에 공유가 되지 않는 이슈 발생

이 정도가 되겠습니다.

로컬 스토리지의 특징을 정확히 알지 못한 상태로 사용하여 생긴 문제였다고 볼 수 있죠.

그래서 해결 방법으로는 두가지를 생각할 수 있었는데,

**메인 도메인과 서버의 로직을 바꿔 쿠키를 활용하는 방안**

이 방법의 경우 서버측의 리소스가 예상되었고, 이미 메인 도메인 클라이언트 측의 로직이 복잡한 상태라서 양 측 모두에게 많은 리소스가 생길 것 같았지만,

클라이언트와 서버간의 데이터 공유의 목적으로 쿠키를 사용하는 것이 더 바람직 하다고 생각했고 이런 단점과 장점이 있었습니다.

장점 : 웹스토리지의 용도에 맞게 사용할수 있다.
단점 : 오래걸릴것 같다.

**쿠키를 이용하여 서브 도메인 간의 토큰 값만 공유하는 방안**

이 방법을 사용하면 이런 흐름을 갖게 됩니다.

기존 메인 도메인에는 로컬스토리지에 토큰이 저장 되어있습니다.
그 토큰을 쿠키에 저장합니다.

쿠키에 저장하게 되면 서브 도메인으로 리다이렉트 되더라도 그 쿠키는 토큰을 가지고 있게 됩니다.

쿠키에 저장 된 값을 이용하여 인증 인가에 사용할 수도 있고,
기존과 같은 방식으로 가려면 그 값을 다시 로컬스토리지에 저장하여 요청을 보내면 됩니다.

장점 : 서버에서 리소스를 투자하지 않고 클라이언트 측에서 해결할 수 있다.
단점 : 보안이 걱정된다 ? 그러나 로컬스토리지나 쿠키 모두 웹스토리지를 이용하는 방법이라 기존의 방법에 비해 크게 문제되지 않는다고 생각했다.

그래서 저는 후자의 방법을 택하였고, 클라이언트 측에서 쿠키를 손쉽게 관리하는 방법을 찾았습니다.

#### react-cookie 사용하기

react-cookie 라이브러리를 사용하여 손쉽게 관리할 수 있습니다.

```
yarn add react-cookie

npm i react-cookie
```

설치 후에
index.tsx or main.tsx에 Provider를 셋팅해줍니다.

```jsx
import ReactDOM from 'react-dom/client';
import App from './App.tsx';
import './styles/index.css';

import { CookiesProvider } from 'react-cookie';


ReactDOM.createRoot(document.getElementById('root')!).render(
      <CookiesProvider>
          <App />
      </CookiesProvider>
);

```

그 후에 util함수를 하나 만들어줄겁니다.

cookie.ts

```typescript
import { Cookies } from 'react-cookie';

const cookies = new Cookies();

interface CookieOptions {
  path?: string;
  expires?: Date;
  maxAge?: number;
  domain?: string;
  secure?: boolean;
  httpOnly?: boolean;
  sameSite?: 'strict' | 'lax' | 'none';
}

export const setCookie = (name: string, value: string, options?: CookieOptions) => {
  return cookies.set(name, value, { ...options });
};

export const getCookie = (name: string) => {
  return cookies.get(name);
};
```

이러면 클라이언트에서 간단하게 쿠키를 관리할 수 있게 됩니다.

사용 예시를 한번 보겠습니다.

제가 하고싶은 것은 서브 도메인 진입 즉시 쿠키에 담긴 토큰값을 로컬 스토리지에 저장해주는 것 입니다.

그런 이유로 main.tsx에 로직을 작성 해주었습니다.

```jsx
import ReactDOM from 'react-dom/client';
import App from './App.tsx';
import './styles/index.css';

import { RecoilRoot } from 'recoil';
import { BrowserRouter } from 'react-router-dom';
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { CookiesProvider } from 'react-cookie';
const queryClinent = new QueryClient();
//여기부터
import { getCookie } from './utils/cookie.ts';

const token = getCookie('Authorization');
console.debug(token);
if (token) {
  localStorage.setItem('Authorization', token);
}
//여기까지

ReactDOM.createRoot(document.getElementById('root')!).render(
  <QueryClientProvider client={queryClinent}>
    <RecoilRoot>
      <CookiesProvider>
        <BrowserRouter>
          <App />
        </BrowserRouter>
      </CookiesProvider>
    </RecoilRoot>
  </QueryClientProvider>,
);

```

이렇게 getCookie(Key)를 입력하여 해당 값을 가져오고 localStorage에 저장해 주었습니다.

**메인 도메인 쿠키**

![](https://velog.velcdn.com/images/qwzx16/post/560bbbc0-50a0-4999-af06-cb49b841cb8a/image.png)

**서브 도메인 쿠키**

![](https://velog.velcdn.com/images/qwzx16/post/a51ce0ea-add3-4ef8-8957-d1630847a62a/image.png)

**서브 도메인 로컬스토리지**

![](https://velog.velcdn.com/images/qwzx16/post/9aaac004-8783-4957-ab82-cfadad440840/image.png)

이렇게 토큰 값을 무사히 공유되었습니다.

유틸 함수 하나를 만들어 두면 어디서든 getCookie, setCookie를 호출하여 손 쉽게 쿠키 관련된 내용을 구현할 수 있습니다!

여기까지 저의 쿠키 사용기였고 passport를 이용한 서버 측에서 쿠키를 생성해 주는 과정과 클라이언트 측에서 유틸 함수로 쿠키를 손쉽게 활용하는 방법을 알 수 있었습니다..

사실 위에 말씀드린 이슈를 발견했을때 팀원분에겐 되겠죠 할수 있겠죠 하며 태연한척 했지만 손은 바쁘게 검색중이였습니다.

뭔가 해결 못할것 같다는 생각이 들었었죠.

하지만 쿠키를 이용해서 생각보다 빨리 해결할수 있었습니다 !
다재다능한 쿠키 문제가 생겼을때 해결에 도움을 줄것이에요 !

원래는 셋 다 알아보려 했는데 너무 길어지네요.

로컬스토리지와 세션스토리지는 다음에 돌아와보겠습니다 !
~~그럼 이만..~~
