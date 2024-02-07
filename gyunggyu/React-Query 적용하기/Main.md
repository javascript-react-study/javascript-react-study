# 나의 프로젝트에서 React-Query 마이그레이션 적용기 !

![Alt text](%EA%B5%AC%EB%A3%A8%EC%9E%901.png)

![Alt text](%EA%B5%AC%EB%A3%A8%EC%9E%902.png)

평론 사이트를 만들면서 고칠것이 아주 많지만 하나를 꼽자면..

서버 요청에 대한 부분이다.

서버 요청을 하면서 axios를 사용했고 useEffect로 필요한 화면에서 함수를 실행하여 받아온 데이터를 useState나 RecoilState에 가공하고 보관하여 보여주었다.

이번에 그리고 여기저기 흩어졌던 요청에 대한 함수를 분류별로 묶어서 관리하게 되었다.

한층 보기 깔끔해졌고 수정하기가 간편해졌는데 이제서야 느꼈다.

기존에 서버와 통신하는 방식을 갈아엎고 React-Query로 마이그레이션이 필요하다는 것을 !

오늘은 그 과정을 담아보겠다.

## 간단하게 React-Query의 사용법에 대해

### GET요청에 대해 처리해주는 useQuery

```jsx
export const fetchTodo = async () => {
  const result = await axios.get(url);
  return result;
};
```

이렇게 만들어진 간단한 GET요청 함수를 만들어 보았다.

```jsx
const query = useQuery({
  queryKey: ['todo'],
  queryFn: fetchTodo,
});

console.log('query!', query);
```

이렇게 하면

![![Alt text](<Query Console.png>)](<쿼리 콘솔.png>)

반환값을 얻어 다양한 속성을 사용할수 있다

> query.data : 데이터 반환
> query.error : 에러 반환

이런 식으로 말이다.

아주 간단하고 다양한 옵션을 제공한다.

### POST , DELETE , PATCH 요청에 대한 useMutate

```jsx
const createReview = async () => {
  const reviewData = {
    user: user,
    review: review,
  };
  const result = await axiosConfig.post('review/create', { reviewData }, { withCredentials: true });

  return result;
};
```

함수를 만들어주었다.

```jsx
const mutate = useMutation({
  mutationFn: createReview,
  onSuccess: () => {
    console.log('요청 성공');
  },
  onError: () => {
    console.error('에러 발생');
  },
  onSettled: () => {
    console.log('결과에 관계 없이 무언가 실행됨');
  },
});
```

Query와의 차이는 queryKey값이 들어가지 않는다.

그래도 아래에는 옵션을 몇개 넣어봤는데,

요청에 대한 성공 유무에 따라 try/catch/finally와 비슷한 동작을 하는 옵션을 추가해줄수 있다.

그 외에 mutate객체로 호출할수 있는 옵션은 이렇게 존재한다.

![Alt text](<뮤테이트 콘솔.png>)

함수를 실행하기 위해선 mutate.mutate 이런식으로 버튼이나 인풋에 호출해주면된다.

이렇게 당장 내게 필요한 정보는 어느정도 얻어냈으니 이제 마이그레이션을 해보겠다.

##마이그레이션

그럼 여기부터 본격 마이그레이션을 해보겠따.

### useQuery 적용기

**기존 코드**

```jsx
//useReviewFetch.js
const [latestReviews, setLatestReviews] = useState([]);

const fetchLatestReviews = async () => {
  const result = await axiosConfig.get('review/latest');
  setLatestReviews(result.data.reviews);
};
//Contents.js
const { fetchLatestReviews, latestReviews } = useReviewFetch();
useEffect(() => {
  fetchLatestReviews();
}, []);
///..
return <CardWrapProvider title={'기존 카드'} cardList={latestReviews} onClick={redirectContent} />;
```

**수정 코드**

```jsx
//useReviewFetch.js
const fetchLatestReviews = async () => {
  const result = await axiosConfig.get('review/latest');
  return result;
};
const query = useQuery({
  queryKey: ['tolatestReviews'],
  queryFn: fetchLatestReviews,
});
//Contents.js
const { query } = useReviewFetch();

return(
  <>
  {
    query.isPending ? (
      <ResponsiveProvider direction={'col'}>
        <p>Loading</p>
      </ResponsiveProvider>
    ) : (
      <CardWrapProvider
        title={'쿼리 카드'}
        cardList={query.data.data.reviews}
        onClick={redirectContent}
      />
    );
  }
</>
)
```

**결과**
![Alt text](<최초 로딩, 이후 로딩 차이.gif>)

리엑트 쿼리가 캐싱을 해줘서 훨씬 매끄럽게 화면에 렌더링 되는 모습을 볼수 있고 서버 데이터를 올바르게 패칭하는 방법을 배우고 적용해볼수 있었다 !

지금은 isPending 속성만 사용하고 있지만 좀 더 다양한 속성을 활용하면 더 많은 기능도 사용할수 있겠다..!

성능에 대한 지표가 숫자로 있으면 좋았겠지만 시각적으로도 월등히 좋아보인다 ! (만족)

### useMutate 적용기

mutate의 경우에는 아직 특별히 사용해본 기능은 없다.

그래도 리뷰 작성 역시 서버로 정보를 보내는 작업이기 때문에 마이그레이션을 해보겠다.

**기존 코드**

```jsx
const createReview = async () => {
  const reviewData = {
    // ... 데이터
  };
  try {
    const result = await axiosConfig.post(
      'review/create',
      { reviewData },
      { withCredentials: true },
    );
    navigator(`/detail/${mediaType}/${contentId}`);
  } catch (err) {
    console.log(err);
  }
};
return <Button label={'발행'} bg={'main'} onClick={createReview} />;
```

**수정 코드**

```jsx
const createReview = async () => {
  // ... 위와 동일
};

const createMutate = useMutation({
  mutationFn: createReview,
  onSuccess: () => {
    console.log('요청 성공');
  },
  onError: () => {
    console.error('에러 발생');
  },
  onSettled: () => {
    console.log('결과에 관계 없이 무언가 실행됨');
  },
});

return <Button label={'발행'} bg={'main'} onClick={createMutate.mutate} />;
```

단순하게 중간에 mutate를 거쳐주는 과정을 거쳤다.

Mutation에서 더욱 개선할수 있는 내용은 낙관적 업데이트인데

예를 들어보자면 장바구니 기능을 생각해볼수 있다.

![Alt text](<낙관적 업데이트 순서도.png>)

이런 기능 구현에 대해서는 앞으로 생각해볼 필요가 있다.

낙관적 업데이트가 무조건 필요하진 않기 때문에 아직은 기본 구현에 마쳤고 기능도 정상적으로 동작하는 것을 확인하였다.

낙관적 업데이트는 이제 ..

콘텐츠에 대해 좋아요나 보고싶어요 버튼으로 만들어줄수 있을것 같다.
사실 고민하던 내용인데 떄마침 해결되어서 구현하는데 더욱 도움이 될것 같다 !

## DevTool

데브툴 적용에 대해서도 간단히 보겠다.

```
yarn add @tanstack/react-query-devtools
```

설치 후에

```jsx
//index.js
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';
import { ReactQueryDevtools } from '@tanstack/react-query-devtools';

const queryClient = new QueryClient();

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <QueryClientProvider client={queryClient}>
    <ReactQueryDevtools />
    <App />
  </QueryClientProvider>,
);

reportWebVitals();
```

QueryClientProvider에 가깝게 배치해준다.

![QueryClientProvider](<데브툴 버튼.png>)

이런 버튼이 나오고 클릭하면

**Querty**
![Alt text](<데브툴 쿼리.png>)
**Mutate**
![Alt text](<데브툴 뮤테이트.png>)

이렇게 다양한 정보를 확인할수 있다.

개발하며 많은 도움을 받을수 있겠다 !

## 결론

useEffect를 올바르게 사용하기 위한 서버 데이터 요청에 대한 캐싱과 요청하는 동안 보여줄 UI에 대해서 고민을 많이 했고, 좋아요 기능을 만들기 위한 낙관적 업데이트에 대한 내용도 익히 들어 어떻게 적용할수 있을까에 대한 고민도 많이 했다..

직접 스테이트를 이용한 구현도 어설프게 되었기 때문에 더욱 머리가 아팠다.

React-Query가 좋다 좋다 익히 들었지만 이렇게 적용하고 하나하나 알아보다 보니 내가 고민하던 서버와의 데이터 통신에 대한 내용을 거의 대부분 해결해주는 라이브러리였다 !

하지만 사용법 뿐 아니라 내부 구조에 대해서 그리고 동작 원리에 대해서도 알아보니 흥미로웠고 직접 캐싱 및 다양한 기능을 구현해보는 경험도 꼭 해보고싶다.

서버 데이터는 React-Query
클라이언트 데이터는 Recoil

앞으로도 잘부탁해 라이브러리들아 고맙다~~~
