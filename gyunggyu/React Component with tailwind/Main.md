## 테일윈드를 사용하는 여러분 ! 서론

안녕하세요
오늘은 테일윈드 css를 파헤쳐보겠습니다

저는 부트스트랩으로 처음 css라이브러리에 입문하였고 테일윈드를 사용하게 된건 홍플릭스 프로젝트를 할 때 였어요

아시다시피 테일윈드는 편리한만큼 단점이 있죠

- 조건식 넣기 까다로움
- 코드 드러워짐

하지만 그에 비해 너무 편하다는 장점이 있었어요 적어도 제게는 말이죠
그래서 테일윈드의 공식문서도 많이 보고 더 효율적이게 사용할수 있는 방법을 찾아서

무엇보다도 많이들 사용하는 **js-in-css** 라이브러리 못지 않게 유연하게 사용할수 있는 방법을 찾기위해 다른 분들의 사용방법도 많이 찾아봤답니다

결국에 저는 보석같은 글을 발견하고 제가 사용하기 편하게 적용해서 개인프로젝트에도 팀프로젝트에도 아주 유용하게 사용하고 있읍니다.

React 스터디에 어울리는 자료인지는 고민을 많이 했지만 ..

React를 사용하며 Component를 효율적으로 만드는것은 아주 중요하고
거기엔 또 CSS가 빠져서는 안되기 때문에 !
저 처럼 테일윈드가 좋은 분들을 위하여 포스팅 합니다

## 어떻게 ?

### 사용하게 될 라이브러리

세가지의 라이브러리를 사용합니다
모두 유틸함수로 만들어서 간편하게 사용하는 방법을 알려드릴테니 역할 정도만 알고 가십죠

#### clsx

```
yarn add clsx
```

https://github.com/lukeed/clsx
이녀석의 역할을 조건부 렌더링이 간편하게 해줍니다 !
공식문서를 확인해보면 삼항연산자와 같은 조건식을 제공해주는 것을 확인할수 있습니다

```jsx
import clsx from 'clsx';

const Button = ({ primary, danger, disabled }) => {
  const buttonClass = clsx('base-button', {
    'primary-button': primary,
    'danger-button': danger,
    'disabled-button': disabled,
  });

  return <button className={buttonClass}>Click me</button>;
};

// 사용 예시
<Button primary disabled />;
// 결과: <button class="base-button primary-button disabled-button">Click me</button>
```

#### cva

```
yarn add class-variance-authority
```

핵심이라 생각하는 cva입니다 !

```jsx
import { cva } from 'class-variance-authority';

const buttonVariants = cva(`p-2 rounded`, {
  variants: {
    size: {
      sm: `text-sm`,
      md: `text-md`,
      lg: `text-lg`,
    },
    color: {
      primary: `bg-blue-500 text-white`,
      secondary: `bg-gray-300 text-gray-700`,
    },
  },
});

const MyButton = ({ size, color, children }) => {
  const className = buttonVariants({ size, color });
  return <button className={className}>{children}</button>;
};

const App = () => {
  return (
    <div>
      <MyButton size='md' color='primary'>
        Click me!
      </MyButton>
      <MyButton size='lg' color='secondary'>
        Another Button
      </MyButton>
    </div>
  );
};
```

위 예시를 보시면 size 및 color의 이름을 지정해주어 변수처럼 사용할수 있습니다.

`bg-blue-500 text-white` 이걸 모두 적는것 보단 `primary`로 땡 치는게 가독성에 좋을 것이고,
확장성도 챙길수 있어요
타일윈드와의 호환도 좋습니다

#### tailwind-merge

```
yarn add tailwind-merge
```

우리는 테일윈드를 사용하며 이런 상황을 겪을수가 있습니다.
상상을 해봅시다 우리는 버튼 컴포넌트를 하나 만들었어요
그런데 그 버튼 컴포넌트에는 다양한 스타일이 적용되어 있겠죠

```jsx
export default function App() {
  return (
    <div className='App'>
      <h1>Hello CodeSandbox</h1>
      <h2>Start editing to see some magic happen!</h2>
      <CustomButton text={'버튼'} addClassName='p-10' />
    </div>
  );
}

export const CustomButton = ({ addClassName, text }) => {
  return <button className={`bg-red-400 px-2 py-1 ${addClassName}`}>{text}</button>;
};
```

여기서 addClassName은 적용되지 않습니다.

```jsx
import { twMerge } from 'tailwind-merge';

function App() {
  return (
    <div>
      <CustomButton text="버튼" addClassName="p-10" />
    </div>
  );
}

export default App;

export const CustomButton = ({ addClassName, text }) => {
  return (
  <button className={twMerge(`bg-red-400 px-2 py-1 ${addClassName}`)}>{text}</button>;
    )
};
```

하지만 twMerge를 이용하면 p-10이 정상적으로 적용 됩니다.
핵심은 마지막에 적용시킨 클래스를 적용시켜주는 역할을 하게됩니다 !
이제 컴포넌트를 따로 수정하지 않아도 추가적으로 부여해주는 className으로 다양한 상황에 대처할수 있겠죠

라이브러리 설명이 길었습니다 우리에겐 세자루의 칼이 생겼고 이제 **js-in-css**의 멱을 따러 갑시다

### 더 복잡한데 어케씀

아주 간단합니다 우리는 유틸함수 하나만 만들면 됩니다.

```ts
import { ClassValue, clsx } from 'clsx';
import { twMerge } from 'tailwind-merge';

export const cn = (...inputs: ClassValue[]) => {
  return twMerge(clsx(inputs));
};
```

TS기준으로 설명하겠습니다
JS경우에는 ClassValue라는 속성 지정 부분만 빼주시면 됩니다
cn이라는 함수는 이제 inputs를 통해 class를 받아와서 twMerge와 clsx를 적용시켜줍니다

여기까지가 셋팅 진짜 끝!

이 유틸함수를 import해서 이제 보본격적으로 컴포넌트를 만들어볼게요

```jsx
import { cn } from '../utils/cn';
import { cva, VariantProps } from 'class-variance-authority';
import { ButtonHTMLAttributes, FC } from 'react';
/**
VariantProps: class-variance-authority 라이브러리에서 제공하는 클래스 변이 관련 기능을 사용하기 위한 타입입니다.
이 타입은 cva 함수를 사용할 때 클래스의 변이를 지정하기 위해 필요한 속성들을 정의하고 있습니다.

ButtonHTMLAttributes: HTML 버튼 엘리먼트에 적용되는 속성들을 정의한 타입입니다.
이는 React에서 HTML 요소의 속성을 타입으로 지정해 놓은 것으로,
버튼 컴포넌트에 HTML 버튼 엘리먼트의 속성들을 적용하기 위해 사용됩니다.

FC: React에서 함수형 컴포넌트를 정의할 때 사용하는 타입으로,
FC는 "Functional Component"의 약자입니다.
이를 사용하면 React 함수형 컴포넌트를 정의할 때 props의 타입이나 기타 컴포넌트 속성들을 명시적으로 지정할 수 있습니다.
*/

export const ButtonVariants = cva(
  //모든 경우에 공통으로 들어갈 CSS
  `
  flex justify-center items-center active:scale-95 rounded-xl
  text-sm font-bold text-slate-100 transition-all shadow-md
  hover:scale-105 duration-200 hover:translate-x-10
  `,
  {
    //variant , size에 따라 다른 디자인을 보여줄수 있다
    variants: {
      variant: {
        default: 'active:scale-100',
        grey: ' bg-slate-300 ',
        blue: ' bg-blue-400',
        red: 'bg-red-400',
      },
      size: {
        default: '',
        md: ' w-[6rem] h-[2rem] text-[1rem] rounded-md',
        lg: 'w-[21rem] h-[7rem] text-[2rem] rounded-3xl',
        wlg: 'w-[24rem] h-[5rem] text-[2rem]',
        rounded: 'w-[6rem] h-[6rem] rounded-full',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'default',
    },
  },
);

interface ButtonProps
  extends ButtonHTMLAttributes<HTMLButtonElement>,
    //Button의 속성을 타입지정을 통해 손쉽게 사용
    VariantProps<typeof ButtonVariants> {
  label?: string;
  //라벨은 단지 string을 넣을때 사용
  children?: React.ReactElement;
  //icon component 같은 리엑트 컴포넌트에 사용
  additionalClass?: string;
  //추가 className
}

/**
 * @variant 색상 지정 ex) gray, blue, red
 * @size 사이즈 지정 md, lg, wlg
 * @children ReactElement 아이콘같은걸 넣어준다
 * @label String을 넣어 버튼 라벨을 지정해준다
 * @additionalClass 추가할 클래스 속성을 넣어준다
 * @props 추가할 버튼 속성을 넣어준다
 */
const Button: FC<ButtonProps> = ({ variant, size, children, label, additionalClass, ...props }) => {
  return (
    <button className={cn(ButtonVariants({ variant, size }), additionalClass)} {...props}>
      {children && children}
      {label && label}
    </button>
  );
};

export default Button;

```

자 버튼 컴포넌트를 만들었어요
저는 템플릿처럼 만들어 두고 여기저기 class만 변경해서 사용한답니다

주석을 천천히 보시다보면 각각의 역할을 아실수 있을거에요

- variant는 색상을 지정해줍니다
- size로는 다양한 크기의 버튼을 만들수 있도록 지정해줍니다
- children과 label은 버튼의 텍스트 혹은 아이콘 컴포넌트를 받아 버튼의 label이 됩니다
- props는 컴포넌트 자체에 버튼 속성이라는 것을 명시해 주었기 때문에 onClick이나 disable같은 버튼 고유의 속성을 손쉽게 사용하게 해줍니다
- additionalClass의 경우 특별한 경우에 버튼의 디자인이 달랐으면 할 때 추가로 className을 지정해주면 그것으로 적용됩니다

이렇게 해서 사용한 예시까지 보시겠습니다
여기까진 장황하고 복잡해 보이는데 사용하면 진짜 편해요 ... 진짜루

몇가지의 버튼을 준비해봤습니다

#### 첫번째 버튼

```jsx
<Button label='Blue' variant={'blue'} size={'lg'} onClick={() => handleClickScroll(0)} />
```

파랗고 큰 버튼입니다
onClick 함수도 ...props속성이 있기 때문에 따로 props를 onClick을 추가해서 만들어줄 필요 없죠

#### 두번째 버튼

```jsx
<Button label='Grey' variant={'grey'} size={'rounded'} onClick={() => handleClickScroll(1)} />
```

회색의 동글한 버튼입니다

#### 세번째 버튼

```jsx
<Button
  label='Red'
  variant={'red'}
  size={'md'}
  additionalClass='hover:bg-red-600 p-10'
  onClick={() => handleClickScroll(4)}
/>
```

추가로 클래스를 부여해준 붉은 버튼입니다

#### 네번째 버튼

```jsx
<Button
  label='다크모드'
  size={'rounded'}
  variant={darkMode ? 'default' : 'grey'}
  onClick={() => setDarkMode(!darkMode)}
/>
```

조건식을 넣어준 동그란 버튼입니다

#### 1,2,3번 버튼에는 스크롤 기능이 있습니다 기능동작 잘하는지만 봐주시면 되겠습니다.

### 시연

![](https://velog.velcdn.com/images/qwzx16/post/b8d99738-46ac-4048-99fd-7c8a90062786/image.gif)

다크모드 버튼 색상을 보면 글씨색이 안바뀌죠
지정 안해줘서 그럽니다
이 또한 clsx가 조건식을 간편하게 사용하게 해주기 때문에 금방 적용시킬수 있겠죠

```jsx
  	additionalClass?: string | boolean;
  // Button.tsx 타입지정 다시 해주기
    <Button
      label='다크모드'
      size={'rounded'}
      variant={darkMode ? 'default' : 'grey'}
      additionalClass={darkMode ? 'text-black' : ''}
      onClick={() => setDarkMode(!darkMode)}
      />
    //조건식 추가 클래스 적어주기
```

![](https://velog.velcdn.com/images/qwzx16/post/8fdd57de-a9ca-49d6-b93c-a519419e87bb/image.gif)
짠

이렇게 길고 긴 글이 끝났습니다

다들 자신만의 방법으로 멋진 컴포넌트를 만들어 멋진 프로젝트 잘하십쇼

참고로 스토리북과의 호환도 좋다 합니다
언젠간 스토리북으로 찾아오겠습니다
~~그럼2000...~~

출처

> - https://xionwcfm.tistory.com/328
