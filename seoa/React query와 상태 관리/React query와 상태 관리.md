# React query와 상태 관리

# 상태?

- 주어진 시간에 대해 시스템을 나타내는 것으로 언제든지 변경될 수 있음
- 즉 문자열, 배열, 객체 등의 형태로 응용 프로그램에 저장된 데이터
  → 개발자 입장에선 관리해야 하는 데이터들!

## 상태를 두 가지로 나누면,

| Client State              | Server State              |
| ------------------------- | ------------------------- |
| Ownership이 Client에 있음 | Ownership이 Server에 있음 |

| - Client에서 소유하며 온전히 제어 가능함

- 초기값 설정이나 조작에 제약사항 없음
- 다른 사람들과 공유되지 않으며 Client 내에서 UI/UX 흐름이나 사용자 인터랙션에 따라 변할 수 있음
- 항상 Client 내에서 최신 상태로 관리됨 | - Client에서 제어하거나 소유되지 않는 원격의 공간에서 관리, 유지됨
- Fetching이나 Updating에 비동기 API가 필요함
- 다른 사람들과 공유되는 것으로 사용자가 모르는 사이에 변경될 수 있음
- 신경 쓰지 않는다면 잠재적으로 ‘out of date’가 될 가능성을 지님 |

# 상태 관리?

- 상태를 관리하는 방법에 대한 것 → 프로덕트가 커짐에 따라 어려움도 커짐
- 상태들은 시간에 따라 변화함
- React에선 단방향 바인딩이므로 Props Drilling 이슈도 존재
- Redux, MobX 같은 라이브러리를 활용해 해결하기도 함

→ 모던 웹 프론트엔드 개발에서는 UI/UX의 중요성과 함께 프로덕트 규모가 많이 커지고, FE에서 수행하는 역할이 늘어남

→ **관리하는 상태가 많아졌기 때문에, 상태 관리의 필요성이 강조되고 있음**

# React query 란?

- 애플리케이션의 데이터를 관리하고 동기화하는데 사용되는 상태 관리 라이브러리

## React query를 왜 사용할까?

1. 간편한 데이터 관리
   - 데이터 가져오기, 캐싱, 동기화 및 업데이트 처리를 간편하게 할 수 있게 해줌
2. 실시간 업데이트 및 동기화
   - 실시간 데이터 업데이트와 자동 동기화를 지원하여 서버와 클라이언트 데이터의 일관성을 유지
3. 데이터 캐싱
   - 데이터를 캐싱하여 불필요한 API 요청을 줄이고 애플리케이션의 성능 향상
4. 서버 상태 관리
   - 서버 상태 관리(예를 들면 로딩 중, 에러, 성공 등의 상태)를 간편하게 처리
5. 간편한 설정
   - 간단한 설정으로 사용 가능

## React query는 어떻게 사용할까?

### 1. 세팅하기

- @tanstack/react-query 설치
- devtools는 선택 사항이며, devtools를 사용하면 패칭한 데이터를 쉽게 관리할 수 있음

  ```jsx
  npm install @tanstack/react-query (@tanstack/react-query-devtools)
  yarn add @tanstack/react-query (@tanstack/react-query-devtools)
  ```

- 최상위 컴포넌트 위에 `QueryClientProvider` 로 감싸줌
- devtools는 최상위 컴포넌트에 최대한 가까운 위치로 배치

  ```jsx
  import { QueryClient, QueryClientProvider } from "@tanstack/react-query";

  const queryClient = new QueryClient();

  function App() {
    return (
      <QueryClientProvider client={queryClient}>
        <Todos />
        <ReactQueryDevtools /> // 선택사항
      </QueryClientProvider>
    );
  }
  ```

- devtools를 설치했다면, 하단에 꽃모양 아이콘을 클릭하면 현재 불러오고 있는 데이터들이 무엇이 있는지 한 눈에 확인할 수 있음
  ![devtools](React query와 상태 관리/devtools.png)

### 2. Query & Mutation

- React query는 API의 요청을 `Query`, `Mutation`의 두 가지로 처리함

1. Query

   - Query 함수를 사용해서 데이터를 가져오고 캐싱할 수 있음
   - Query 함수는 데이터 패칭용으로 사용되며, 보통은 GET으로 받아오는 대부분의 API에서 `useQuery()` 함수를 사용함(Reading에만 사용)

     ```jsx
     const { data } = useQuery(queryKey, queryFunction, options);
     ```

     - 첫번째 인자: Query Key로 응답 데이터를 캐싱할 때 사용되는 Unique key
     - 두번째 인자: Query Function으로 Promise를 반환하는 함수, 쿼리 요청을 수행하기 위한 fetch, axios 등의 함수를 의미함
     - 세번째 인자: useQuery에 사용되는 옵션을 지정하는 객체

   - useQuery로 받을 수 있는 리턴값

     - `data`: 마지막으로 성공한 resolved된 데이터(Response)
     - `error`: 에러가 발생했을 때 반환되는 객체
     - `isFetching`: Request가 in-flight 중일 때 true
     - `status`, `isLoading`, `isSuccess`, `isLoading` 등: 현재 query의 상태
     - `refetch`: 해당 query refetch하는 함수 제공
     - `remove`: 해당 query cache에서 지우는 함수 제공

   - useQuery 옵션
     - `onSuccess`, `onError`, `onSettled`: query fetching 성공/실패/완료 시 실행할 Side Effect 정의
     - `enabled`: 자동으로 query를 실행시킬지 말지 여부
     - `retry`: query 동작 실패 시, 자동으로 retry 할지 결정하는 옵션
     - `select`: 성공 시 가져온 data를 가공해서 전달
     - `keepPreviousData`: 새롭게 fetching 시 이전 데이터 유지 여부
     - `refetchInterval`: 주기적으로 refetch 할지 결정하는 옵션
   - user의 프로필 정보를 가져오는 예시

     ```jsx
     import { useQuery } from 'react-query';

     // 데이터 패칭 함수
     const getUserData(userId) = () => {
       return fetch(`/api/user/${userId}`).then((response) => response.json());
     }

     const UserProfile = ({ userId }) => {
     	// useQuery 를 사용하여 데이터를 가져옴, ['user', userId]는 쿼리 키
       const { data, isError, error, isLoading } = useQuery(['user', userId], () => fetchUserData(userId));

       if (isLoading) {
     	// isLoading을 사용하여 데이터가 로딩중일 때 화면을 랜더링
         return <div>Loading...</div>;
       }

       if (isError) {
     	// isError를 사용하여 error가 발생할 때 화면을 랜더링
         return <div>Error: {error.message}</div>;
       }

       return (
         <div>
           <h1>User Profile</h1>
           <p>Name: {data.name}</p>
           <p>Email: {data.email}</p>
         </div>
       );
     }
     ```

2. Mutation

   - Mutation 함수는 데이터와 캐시를 업데이트 할 수 있음
   - 데이터 생성, 수정, 삭제용으로 사용되며, POST, PUT, DELETE 요청시에 사용(Create, Update, Delete에 사용)

     ```jsx
     const { mutate } = useMutation(mutationFn, options);
     ```

     - 첫번째 인자: Mutation Function으로 Promise를 반환하는 함수, useQuery에서와 마찬가지로 fetch, axios 등의 함수를 의미함
     - 두번째 인자: useMutation에 사용되는 옵션을 지정하는 객체(Query Key를 설정해 줄수도 있으며, Query Key를 지정하게 되면 devtool에서 볼 수 있음)

   - useMutation을 사용하여 사용자의 정보를 업데이트하는 예시

     ```jsx
     import { useMutation, useQueryClient } from "react-query";

     // 데이터 업데이트 함수
     function updateUser(userId, updatedData) {
       return fetch(`/api/user/${userId}`, {
         method: "PUT",
         body: JSON.stringify(updatedData),
       }).then((response) => response.json());
     }

     function UserProfileEditor({ userId }) {
       const queryClient = useQueryClient();

       const { mutate } = useMutation(
         (updatedData) => updateUser(userId, updatedData),
         {
           onSuccess: () => {
             // 데이터 업데이트 후 캐시를 재로드
             queryClient.invalidateQueries(["user", userId]);
           },
         }
       );

       const handleSubmit = (updatedData) => {
         mutate(updatedData); // mutate는 자동으로 실행되지 않기 때문에 submit 시에 mutate 실행
       };

       return (
         <div>
           <h2>Edit User Profile</h2>
           <UserForm onSubmit={handleSubmit} />
         </div>
       );
     }
     ```

### 3. 데이터 캐싱

- `staletime`, `cachetime` 옵션을 사용하면 React Query에서 자체적으로 제공하는 데이터 캐싱 기능을 이용할 수 있음
- 캐싱을 활용하여 불필요한 API 호출을 줄임으로써 전체적인 애플리케이션 성능을 향상시킬 수 있음

1. staletime(갱신 지연 시간)
   - staletime은 캐시된 데이터의 유효 기간을 나타내는 옵션
   - 호출한 데이터는 리액트 쿼리 자체적으로 저장하는데, staletime은 기본적으로 0으로 설정되어 있어 데이터가 한 번 캐시되면 즉시 만료되고 다시 요청됨
   - 이 값을 조정해서 테이터를 일정 시간 동안 캐시로 사용하고, 그 이후에만 다시 요청하도록 할 수 있음
   - 쉽게 말해서, 데이터의 유통 기한을 정해준다고 할 수 있음
     ```jsx
     const { data } = useQuery(['data', getServerData,{
       staletime: 10 * 60 * 1000;  // data 를 10분 동안 캐시로 사용
     })
     ```
     - 자주 변경되지 않는 데이터의 경우 캐시된 데이터를 사용함으로써 불필요한 네트워크 요청을 줄이고, 애플리케이션의 성능을 향상 시킴
2. cachetime(캐시 유지 시간)
   - cachetime은 캐시된 데이터가 얼마나 오랫동안 메모리에 유지될지를 나타내는 옵션
   - 이 값을 설정하면 캐시된 데이터가 일정 시간 동안 메모리에 유지된 후 자동으로 삭제됨
     ```jsx
     const { data } = useQuery(['data', getServerData,{
       cachetime: 30 * 60 * 1000, // data 를 30분 동안 캐시로 유지함
     })
     ```
     - 데이터가 일정 기간 동안 메모리에 남아있게 함으로써 같은 데이터를 다시 요청할 때 캐시에서 가져오기 때문에 성능을 향상시킬 수 있음

# React query가 상태 관리 라이브러리를 대체할 수 있을까?

- **React query**는 서버 상태를 다루는 라이브러리
- **Redux**, **Mobx** 등은 클라이언트 상태를 다루는 라이브러리
- 전역 상태 관리 라이브러리가 아니라 서버와 클라이언트 간의 비동기 작업을 쉽게 해주는 라이브러리
- **React query**를 도입하면 개발자가 전역적으로 관리해야하는 데이터는 매우 적어짐
- 과거 리액트 개발자들은 서버에서 데이터를 받아오는 작업을 리덕스에서 처리하기 위해 `redux-thunk`, `redux-saga` 등을 이용해서 비동기 작업을 구행하고 데이터를 리덕스 스토어에 저장한 뒤, 그 데이터를 각 컴포넌트에서 사용함
- **React query**는 이 작업을 매우 간편하게 만들어 주는데다가 데이터 캐싱을 아주 쉽게 해결할 수 있음
- 쿼리에 `staleTime: Infinity`, `cacheTime: Infinity` 옵션만 추가하면 앱을 끄기 전까지 다시 fetch 되지 않는 데이터로 만들 수 있음
- **React query**에서 모든 상태값과 메서드를 제공하기 때문에, 데이터가 처음 fetch 되는 동안 `isLoading` 등의 상태를 직접 선언하고 조작할 필요도 없음
- 물론 서버 데이터와 관계없이 전역적으로 다뤄야하는 데이터들, 예를 들어 테마, 사용자, 로케일 데이터는 클라이언트에서만 다루는 데이터임
- 따라서, React query에 임의로 저장하고 다루는 것이 아니라 `Context`나 전역 상태 관리 라이브러리를 사용해서 핸들링 해야함
- 현재 가장 추천하는 조합은 **React-query + recoil**
- `Context`가 가지고 있는 여러가지 이슈가 있기 때문에 전역 상태 관리 라이브러리 하나는 사용을 해야 개발이 편한데, 성능 이슈 없이 간단하게 사용할 수 있기 때문에 **recoil**을 추천함

---

출처

- [https://yrnana.dev/post/2021-08-21-react-query-redux/](https://yrnana.dev/post/2021-08-21-react-query-redux/)
- [https://velog.io/@godud2604/React-Query와-상태관리](https://velog.io/@godud2604/React-Query%EC%99%80-%EC%83%81%ED%83%9C%EA%B4%80%EB%A6%AC)
- [https://musma.github.io/2023/09/14/react-query.html](https://musma.github.io/2023/09/14/react-query.html)
