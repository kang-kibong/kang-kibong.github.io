---
layout: post
title: "React Query와 Concurrent UI Pattern으로 서버 상태 효율적으로 관리하기"
category: frontend
---

<br />
<br />
<br />

# **React Query란?**

![image.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F1171dd7d-6267-4210-b9f4-08941ea21f85%2Fimage.png?id=938788ad-2cf8-433e-919a-67acc17a38ba&table=block)

<br />

**React Query**는 서버 상태를 관리하는 라이브러리로, 데이터 페칭, 캐싱, 동기화 및 서버 상태의 업데이트를 손쉽게 처리할 수 있도록 돕는다.

React 애플리케이션에서 서버와의 통신을 더욱 효과적으로 관리하고, 사용자 경험을 향상시키는 데 중점을 둔 도구이다.

이번 포스팅에서는 React Query가 제공하는 주요 기능들과 이를 사용하는 방법에 대해 다룰 예정이다. 또한, Concurrent UI Pattern을 통한 데이터 페칭에 대해서도 함께 살펴보자.

<br />
<br />
<br />

# 설치

React Query를 사용하기 위해서는 먼저 라이브러리를 설치해야 한다.

사용하는 패키지 매니저에 따라서, 다음 명령어를 통해 설치할 수 있다.

```bash
npm i @tanstack/react-query

pnpm add @tanstack/react-query

yarn add @tanstack/react-query

bun add @tanstack/react-query
```

<br />
<br />
<br />

# QueryClient, QueryClientProvider 설정하기

React Query를 통해 애플리케이션 전반에서 쿼리를 관리하고, 데이터를 자동으로 캐싱하고 동기화하려면 먼저, **QueryClient**와 **QueryClientProvider**를 설정해야 한다.

아래는 `index.tsx` 또는 `App.tsx` 파일에 필요한 설정 코드이다.

```tsx
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import App from "./App";

const queryClient = new QueryClient();

ReactDOM.render(
  <QueryClientProvider client={queryClient}>
    <App />
  </QueryClientProvider>,
  document.getElementById("root")
);
```

<br />
<br />
<br />

# useQuery, useMutation 톺아보기

## [useQuery](https://tanstack.com/query/latest/docs/framework/react/reference/useQuery#usequery)

`useQuery`는 데이터를 **GET** 요청으로 가져올 때 사용하는 훅이다.

해당 훅을 사용하면 데이터를 비동기적으로 페칭하고, 로딩 상태와 에러 상태를 관리하며, 캐싱된 데이터를 활용할 수 있다.

```tsx
const { isLoading, isError, error, data } = useQuery<
  RankingProductsResponse,
  AxiosError
>({
  queryKey: ["rankingProducts", selectedTarget, selectedWish],
  queryFn: () =>
    getRankingProducts({ targetType: selectedTarget, rankType: selectedWish }),
});
```

- **`queryKey`**: 쿼리를 고유하게 식별하는 키이다. 이 키를 통해 React Query는 쿼리의 캐시를 관리하고, 동일한 키를 사용하는 쿼리에서 데이터를 재사용할 수 있다.
- **`queryFn`**: 데이터를 페칭하는 비동기 함수이다. 서버로부터 데이터를 가져오고, React Query는 이 데이터를 캐싱하고 관리한다.

<br />

## [useMutation](https://tanstack.com/query/latest/docs/framework/react/reference/useMutation#usemutation)

`useMutation`은 데이터를 **POST**, **PUT**, **DELETE** 등의 요청으로 변경할 때 사용하는 훅이다.

주로 서버의 데이터를 업데이트하거나, 새로운 데이터를 추가하거나, 기존 데이터를 삭제하는 작업에 사용된다.

```tsx
const { mutate } = useMutation<AddWishResponse, AxiosError, AddwishRequest>({
  mutationFn: (request: AddWishRequest) => addWish(request),
});
```

- **`mutationFn`**: 서버에 변경 요청을 보내는 비동기 함수이다. 이 함수는 서버 상태를 업데이트하며, React Query는 이 변화를 자동으로 관리한다.

<br />
<br />
<br />

# React Query 사용해보기

React Query를 실제 프로젝트에서 어떻게 사용할 수 있는지 살펴보자.

먼저 `useQuery`를 사용해 서버로부터 데이터를 가져오는 방법과 `useMutation`을 사용해 서버에 데이터를 추가하는 방법을 예시로 들어 설명해보겠다.

예시로는, 사용자에게 관심목록 데이터를 가져오고, 관심 목록 아이템을 추가하는 예시로 React Query의 사용법을 보다 쉽게 이해해보도록 하자.

<br />

## useQuery 사용 예시: 관심목록 데이터 가져오기

아래 예제에서는 `useQuery`를 사용하여 서버로부터 관심목록 데이터를 페칭해보려고 한다.

```tsx
import { AxiosResponse, AxiosError } from "axios";
import { axiosInstance } from "@apis/instance";
import { useQuery, UseQueryResult } from "@tanstack/react-query";

// apis/path.ts
export const API_BASE = "/api";
export const WISH_PATHS = {
  GET_WISH: (params: GetWishesRequest) =>
    `${API_BASE}/wishes?page=${params.page}&size=${params.size}&sort=${params.sort}`,
  ADD_WISH: `${API_BASE}/wishes`,
  DELETE_WISH: `${API_BASE}/wishes`,
};

// types/requestTypes.ts
export interface GetWishesRequest {
  page: number;
  size: number;
  sort: string;
}

// types/responseTypes.ts
export interface GetWishesResponse {
  totalPages: number;
  totalElements: number;
  size: number;
  content: Wish[];
  number: number;
  sort: Sort;
  first: boolean;
  last: boolean;
  numberOfElements: number;
  pageable: Pageable;
  empty: boolean;
}

const getWishes = async (
  params: GetWishesRequest
): Promise<GetWishesResponse> => {
  const res: AxiosResponse<GetWishesResponse> = await axiosInstance.get(
    WISH_PATHS.GET_WISH(params)
  );
  return res.data;
};

export default getWishes;

export const useGetWishes = (
  params: GetWishesRequest
): UseQueryResult<GetWishesResponse, AxiosError> => {
  return useQuery<GetWishesResponse, AxiosError>({
    queryKey: ["wishes", params],
    queryFn: () => getWishes(params),
  });
};
```

위 코드에서는 Axios를 사용해 관심목록 데이터를 가져오는 `getWishes` 함수를 정의하고, `useQuery`를 사용하여 이를 React Query와 결합한 `useGetWishes` 훅을 작성하였다.

해당 훅은 쿼리 키(`['wishes', params]`)를 기반으로 데이터를 캐싱하도록 하고, 필요할 때마다 서버에서 데이터를 다시 가져오도록 할 수 있다.

이렇게 구현한 `useGetWishes` 훅을 실제 관심 목록 데이터를 렌더링하는 역할을 하는 `WishList` 컴포넌트에서 사용해보자.

<br />

```tsx
...
import { useGetWishes } from '@apis/wish/hooks/useGetWishes';

export default function WishList() {
  const { data, refetch } = useGetWishes({ page: 0, size: 10, sort: 'asc' });

  return (
    <CenteredContainer maxWidth="md">
      <WishListTitle>관심목록</WishListTitle>
      <WishListContainer>
        {data?.content.map((wishItem) => (
          <WishListItem
            key={wishItem.wishId}
            wishId={wishItem.wishId}
            productImageUrl={wishItem.productImageUrl}
            productName={wishItem.productName}
            productPrice={wishItem.productPrice}
            onDelete={() => refetch()}
          />
        ))}
      </WishListContainer>
    </CenteredContainer>
  );
}
```

이전에 정의한 `useGetWishes` 쿼리 훅으로 서버로부터 관심목록 데이터를 페칭하여 `data`를 받아내고, `data`가 존재할 경우, 해당 데이터를 렌더링하도록 구현하였다.

추가로, `WishListItem` 컴포넌트는 특정 관심 목록 아이템을 삭제할 수 있는 기능이 포함되어 있는데, 사용자가 해당 아이템을 삭제했을 경우, `refetch` 하여 데이터를 새롭게 갱신할 수 있도록 구현해보았다.

<br />

## useMutation 사용 예시: 관심목록에 아이템 추가하기

이번에는 `useMutation`을 사용해 서버에 데이터를 추가해보려고 한다.

```tsx
import { AxiosResponse, AxiosError } from "axios";
import { axiosInstance } from "@apis/instance";
import { useMutation, UseMutationResult } from "@tanstack/react-query";

// apis/path.ts
export const API_BASE = "/api";
export const WISH_PATHS = {
  GET_WISH: (params: GetWishesRequest) =>
    `${API_BASE}/wishes?page=${params.page}&size=${params.size}&sort=${params.sort}`,
  ADD_WISH: `${API_BASE}/wishes`,
  DELETE_WISH: `${API_BASE}/wishes`,
};

// types/requestTypes.ts
export interface AddWishRequest {
  productId?: number;
}

// types/responseTypes.ts
export interface AddWishResponse {
  id: number;
  productId: number;
}

const addWish = async (request: AddWishRequest): Promise<AddWishResponse> => {
  const res: AxiosResponse<AddWishResponse> = await axiosInstance.post(
    WISH_PATHS.ADD_WISH,
    request
  );
  return res.data;
};

export const useAddWishMutation = (): UseMutationResult<
  AddWishResponse,
  AxiosError,
  AddWishRequest
> => {
  return useMutation<AddWishResponse, AxiosError, AddWishRequest>({
    mutationFn: (request: AddWishRequest) => addWish(request),
  });
};
```

마찬가지로 Axios를 통해 관심목록 아이템을 추가하는 `addWish` 함수를 정의하고, `useMutation`을 사용하여 이를 React Query와 결합한 `useAddWishMutation` 훅을 작성하였다.

이후, 해당 `useAddWishMutation` 훅을 통해 관심목록에 아이템을 추가하는 `WishButton` 컴포넌트에서 사용해보도록 하자.

<br />

```tsx
...
import { useAddWishMutation } from '@apis/wish/hooks/useAddWish';

const SUCCESS_ADD_WISH_ALERT = '관심등록 완료';
const FAIL_ADD_WISH_ALERT = '관심등록 실패';

export default function WishButton() {
  const { productId } = useParams<{ productId: string }>();
  const { mutate } = useAddWishMutation();

  const handleClick = () => {
    mutate(
      { productId: Number(productId) },
      {
        onSuccess: () => alert(SUCCESS_ADD_WISH_ALERT),
        onError: () => alert(FAIL_ADD_WISH_ALERT),
      },
    );
  };

  return (
    <Button theme="primary" size="small" onClick={handleClick}>
      관심목록 추가하기
    </Button>
  );
}
```

`WishButton` 컴포넌트는 사용자가 **‘관심목록 추가하기’** 버튼을 클릭하면 `handleClick`함수를 호출하여 `mutate` 함수를 호출하도록 구현하였다.

요청이 성공하면 `onSuccess`를 통해 성공 알림을 표시하고, 실패하면 `onError`를 통해 에러 메시지를 보여주도록 하였다.

<br />
<br />
<br />

# Concurrent UI Pattern을 통해 **선언적으로 데이터 패칭하기**

이전에 Axios를 사용해 비동기 데이터를 처리할 때, `useFetch` 같은 커스텀 훅을 만들어 비동기 상태를 관리해본 경험이 있다.

(해당 내용은 이전에 [\*\*API 통신부터 에러 핸들링까지(feat. Axios)](https://kang-kibong.github.io/api-%ED%86%B5%EC%8B%A0%EB%B6%80%ED%84%B0-%EC%97%90%EB%9F%AC-%ED%95%B8%EB%93%A4%EB%A7%81%EA%B9%8C%EC%A7%80-feat-axios.html)\*\* 포스팅에서 자세하게 살펴볼 수 있다.)

하지만, 이러한 방식은 명령형 프로그래밍 스타일로, 비동기 상태에 따른 다양한 분기 처리를 직접 구현해야 하는 단점이 있다.

반면, React Query는 이러한 복잡한 분기 처리를 줄이고, 선언적 방식으로 비동기 로직을 처리할 수 있게 해준다.

이를 **선언적 데이터 페칭**이라 하며, **Concurrent UI Pattern**의 일환으로 불린다.

해당 패턴은 비동기 작업의 복잡성을 줄여주고, 코드의 가독성과 유지보수성을 크게 향상시킬 수 있다.

우리는 이 패턴을 살펴보기 전에, **tanstack-query v5**에서 새롭게 추가된 `useSuspenseQuery` 훅에 대해 먼저 알아보자.

<br />

## [**useSuspenseQuery**](https://tanstack.com/query/latest/docs/framework/react/reference/useSuspenseQuery#usesuspensequery)

React Query의 `useSuspenseQuery` 훅은 비동기 데이터를 쉽게 처리할 수 있게 해주는 도구이다. 해당 훅은 `Suspense`와 함께 사용되기 때문에 데이터 로딩 상태를 더욱 직관적으로 관리할 수 있다.

```tsx
import { useSuspenseQuery } from "@tanstack/react-query";

export const useGetProductsDetail = ({ productId }: ProductDetailRequest) =>
  useSuspenseQuery<ProductDetailResponse, AxiosError>({
    queryKey: ["productDetail", productId],
    queryFn: () => getProductsDetail({ productId }),
  });
```

<br />

이제 `Suspense`와 `ErrorBoundary`를 결합하여 비동기 작업과 에러 처리를 어떻게 선언적으로 처리할 수 있는지 살펴보자. 그 전에 `react-error-boundary`를 설치하여 에러 처리를 위한 준비를 해야 한다.

```bash
npm install react-error-boundary
```

<br />

## AsyncBoundary 컴포넌트 구현해보기

우리는 `AsyncBoundary` 컴포넌트를 구현하여 `Suspense`와 `ErrorBoundary`를 결합하여 비동기 데이터 로딩과 에러 처리를 더욱 간단하게 관리할 수 있다.

```tsx
import type { ComponentProps, Ref } from "react";
import React, {
  forwardRef,
  Suspense,
  useImperativeHandle,
  useRef,
} from "react";
import type { ErrorBoundaryProps } from "react-error-boundary";
import { ErrorBoundary } from "react-error-boundary";

type Props = {
  children: React.ReactNode;
  rejectedFallback?: ErrorBoundaryProps["fallback"];
  pendingFallback?: ComponentProps<typeof Suspense>["fallback"];
};

interface ResetRef {
  reset?(): void;
}

const AsyncBoundary = forwardRef(
  (
    { pendingFallback, rejectedFallback, children }: Props,
    resetRef: Ref<ResetRef>
  ) => {
    const ref = useRef<ErrorBoundary | null>(null);

    useImperativeHandle(resetRef, () => ({
      reset: () => ref.current?.resetErrorBoundary(),
    }));

    return (
      <ErrorBoundary ref={ref} fallback={rejectedFallback ?? <></>}>
        <Suspense fallback={pendingFallback ?? <></>}>{children}</Suspense>
      </ErrorBoundary>
    );
  }
);

export default AsyncBoundary;
```

먼저 **에러 핸들링**을 살펴보자. `react-error-boundary`를 사용하면 비동기 작업에서 발생하는 에러를 간단히 관리할 수 있다. 이를 위해 `ErrorBoundary` 컴포넌트를 사용하며, `rejectedFallback` 속성으로 에러 발생 시 표시할 컴포넌트를 지정한다.

다음으로, **로딩 상태 관리**를 위해 React 16.6에서 도입된 `Suspense`를 사용한다. `Suspense`는 비동기 작업이 진행 중일 때 보여줄 컴포넌트를 지정할 수 있게 해준다. `pendingFallback` 속성에 로딩 중에 표시할 컴포넌트를 전달하면, 비동기 작업이 완료될 때까지 해당 컴포넌트가 렌더링된다.

마지막으로, **상태 초기화**를 위해 `useImperativeHandle`을 사용한다. `useImperativeHandle`을 통해 외부 컴포넌트에서 내부의 특정 메서드나 상태를 제어할 수 있게 된다. `reset` 함수를 호출하여 `ErrorBoundary`를 초기하여 에러 상태에서 다시 시도할 수 있는 기능을 제공할 수 있도록 구현한다.

<br />

## AsyncBoundary 컴포넌트 사용해보기

이제 `AsyncBoundary`로 비동기 작업을 처리하면서 로딩 상태와 에러를 관리하는 방법을 살펴보자.

예를 들어, 상품 상세 정보를 가져오는 `Gift` 컴포넌트에서 해당 컴포넌트를 활용해보자.

```tsx
...
import { useGetProductsDetail } from '@apis/products/hooks/useGetProductsDetail';
import { Spinner, ErrorMessage } from '@components/common'

interface GiftProps {
	productId: string
}

const ERROR_MESSAGE = 'Failed to load product details.'

export default function Gift({ productId }: GiftProps) {
  const { data } = useGetProductsDetail({ productId });

  return (
    <GiftContainer>
      <AsyncBoundary
        pendingFallback={<Spinner />}
        rejectedFallback={<ErrorMessage message={ERROR_MESSAGE} />
      }>
        <Image src={data?.imageUrl} />
        <div>
          <GiftName>{data?.category.name}</GiftName>
          <GiftInfo>
            {data?.name}
          </GiftInfo>
        </div>
      </AsyncBoundary>
    </GiftContainer>
  );
}
```

`Gift` 컴포넌트에서 `useGetProductsDetail` 훅을 통해 데이터를 페칭하고, 이미지 경로와 상품 카테고리 이름, 상품 이름을 렌더링하고 있다.

이 부분을 `AsyncBoundary`로 래핑하여, 로딩 중일 때는 `Spinner`를, 에러가 발생했을 때는 `ErrorMessage`를 출력하도록 한다.

<br />
<br />
<br />

# 마치면서

이번 포스팅에서는 **React Query**를 사용해 서버 상태를 관리하는 방법을 전반적으로 살펴보았다.

`useQuery`와 `useMutation`을 통해 데이터를 효율적으로 페칭하고, 서버 상태를 손쉽게 업데이트할 수 있는 방법을 배웠다.

또한, **Concurrent UI Pattern**을 통해 비동기 로직을 선언적으로 처리하여, 코드의 가독성과 유지보수성을 높이는 방법도 알아보았다.

React Query는 서버 상태 관리에 있어 매우 강력한 도구이다.

이를 잘 활용하면 애플리케이션의 성능과 사용자 경험을 크게 향상시킬 수 있다.

<br />

독자분들도 해당 포스팅을 통해 관리하고 있는 프로젝트에 React Query를 활용하여 서버 상태 관리 도입을 적극적으로 고려해보길 바란다.
