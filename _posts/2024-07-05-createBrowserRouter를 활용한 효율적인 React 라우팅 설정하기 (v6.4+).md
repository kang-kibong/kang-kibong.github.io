---
layout: post
title: "createBrowserRouter를 활용한 효율적인 React 라우팅 설정하기 (v6.4+)"
category: frontend
---

<br />
<br />
<br />

# react-router-dom이란?

![image.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fb8b4a51c-b87f-4030-af06-587240feeacd%2Fimage.png?id=5d5176d9-df35-4b38-b39d-5444d4398e05&table=block)

<br />

`React Router Dom`은 React 애플리케이션에서 클라이언트 사이드 라우팅을 구현하는 데 사용되는 라이브러리이다.

페이지 간에 네비게이션을 할 수 있으며, URL에 따라 서로 다른 컴포넌트를 렌더링할 수 있게 해준다.

`React Router Dom`은 SPA(Single Page Application)에서 특히 유용하고, 다양한 경로와 그에 따른 컴포넌트 렌더링을 간단하게 설정할 수 있게 해준다.

<br />
<br />
<br />

# react-router-dom 설치

React Router Dom을 사용하려면 먼저 프로젝트에 해당 패키지를 설치해야한다.

아래는 `npm` 패키지 매니저를 사용한 설치 예시이다.

```bash
npm install react-router-dom
```

<br />
<br />
<br />

# [**create-browser-router**](https://reactrouter.com/en/main/routers/create-browser-router) 방식 (v6.4 이후)

![스크린샷 2024-08-10 오후 4.29.02.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fe2aeb1a1-1cfb-42e0-8338-54b843e610d2%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-08-10_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.29.02.png?id=f02531fc-8d1e-4d8c-8f3d-947a9e46f2f0&table=block)

React Router v6.4부터 도입된 `createBrowserRouter`를 사용하면, 라우팅 설정을 보다 선언적이고 명확하게 정의할 수 있다.

공식 문서에 따르면, `createBrowserRouter`는 라우팅 설정을 정의한 후, `RouterProvider` 컴포넌트에 `router`로 전달하여 사용된다는 것을 알 수 있다.

아래의 코드는 `createBrowserRouter`를 사용한 간단한 예시이다.

```tsx
import { createBrowserRouter, RouterProvider } from 'react-router-dom';

const router = createBrowserRouter([
    {
        path: '/',
        element: <MainPage />,
    },
    {
        path: '/byeonghyeon',
        element: <Byoenghyeon />,
    },
    ...
]);

<RouterProvider router={router} />;

```

위 코드는 `createBrowserRouter`는 각 경로(path)에 대해 대응하는 컴포넌트를 설정한다.

예를 들어, 사용자가 `/byeonghyeon` 경로로 이동하면 `Byeonghyeon` 컴포넌트가 렌더링된다.

`RouterProvider` 해당 라우터를 애플리케이션에 제공하는 역할을 한다.

<br />

## 폴더구조

라우팅을 효율적으로 관리하기 위해 프로젝트의 폴더 구조를 아래와 같이 구성해볼 수 있다.

```tsx
routes
├── components
│   └── PrivateRoute
│       └── index.tsx
├── path.ts
└── router.tsx
```

해당 폴더 구조는 라우터와 관련된 모든 설정을 `routes` 디렉토리 아래에 모아 관리하도록하였다.

경로에 대한 상수는 `path.ts`에서 관리하고, `components` 디렉토리에는 특정 조건에 부합해야 접근할 수 있는 컴포넌트들을 배치하도록 하였다.

예를 들어, `PrivateRoute` 컴포넌트는 인증이 필요한 경로를 보호하는 컴포넌트로, 해당 디렉토리에서 관리할 수 있을 것 같다.

`PrivateRoute` 컴포넌트의 역할에 관해서는 이후에 자세히 나올예정이다.

<br />

## `ROUTE_PATH` 정의 (`path.ts`)

라우트 경로는 추후에 경로가 변경될 수 있기 때문에, 하드코딩 하기보다 상수로 정의하여 관리하는 것이 좋다고 판단하였다.

필자는 `ROUTE_PATH`라는 객체에 각 라우트에 대한 경로를 상수로 정의하였다.

```tsx
export const ROUTE_PATH = {
  HOME: "/",
  THEME: "/theme/:themeKey",
  LOGIN: "/login",
  MY_ACCOUNT: "/my-account",
};
```

<br />

## `App` 컴포넌트에 Outlet 설정

```tsx
import AuthProvider from "@context/auth/AuthProvider";
import FilterProvider from "@context/filter/FilterProvider";
import GlobalStyles from "@assets/styles";
import { Outlet } from "react-router-dom";

function App() {
  return (
    <FilterProvider>
      <AuthProvider>
        <GlobalStyles />
        <Outlet />
      </AuthProvider>
    </FilterProvider>
  );
}

export default App;
```

`react-router-dom`의 `Outlet` 컴포넌트를 사용하여 자식 라우트들을 렌더링할 수 있도록 지정한다.

여기서 `Outlet` 컴포넌트는 현재 경로에 따라 자식 컴포넌트를 렌더링하는 역할을 한다.

예를 들어, 사용자가 `/home` 경로로 이동하면, `App` 컴포넌트 내의 `Outlet`은 `Home` 컴포넌트를 렌더링하게 된다.

`App` 컴포넌트는 애플리케이션의 공통 레이아웃을 관리하는 역할을 하기 때문에, 해당 `App` 컴포넌트에서 `Outlet` 컴포넌트를 사용해서 자식 라우트들이 렌더링될 위치를 지정하였다.

<br />

## PrivateRoute 컴포넌트 작성

`PrivateRoute` 컴포넌트는 인증이 필요한 경로를 보호하는 데 사용된다. 예를들어, 인증되지 않은 사용자는 로그인 페이지로 리디렉션되도록 설정할 수 있다.

아래 코드에서는 `useAuth` 훅을 사용해 사용자의 인증 상태를 확인하고, 인증되지 않은 경우 로그인 페이지로 리다이렉션 될 수 있도록 구현하였다.

```tsx
import React from "react";
import { useAuth } from "@context/auth/useAuth";
import { ROUTE_PATH } from "@routes/path";
import { Navigate, Outlet } from "react-router-dom";

export default function PrivateRoute() {
  const { isAuthenticated } = useAuth();

  return isAuthenticated ? <Outlet /> : <Navigate to={ROUTE_PATH.LOGIN} />;
}
```

<br />

## router 작성

마지막으로, 전체 애플리케이션의 라우팅을 관리하는 `router.tsx` 파일을 작성해보았다.

```tsx
import React from "react";
import { createBrowserRouter } from "react-router-dom";
import { App, Home, Theme, Login, MyAccount } from "@pages/index";
import PrivateRoute from "./components/PrivateRoute";
import { ROUTE_PATH } from "./path";

export const router = createBrowserRouter([
  {
    path: ROUTE_PATH.HOME,
    element: <App />,
    children: [
      {
        path: ROUTE_PATH.HOME,
        element: <Home />,
      },
      {
        element: <PrivateRoute />,
        children: [
          {
            path: ROUTE_PATH.THEME,
            element: <Theme />,
          },
          {
            path: ROUTE_PATH.MY_ACCOUNT,
            element: <MyAccount />,
          },
        ],
      },
      {
        path: ROUTE_PATH.LOGIN,
        element: <Login />,
      },
    ],
  },
]);
```

`App` 컴포넌트를 루트로 설정하고, 그 아래에 여러 자식 라우트를 정의하였다.

이후, 인증이 필요한 경로에는 `PrivateRoute` 컴포넌트의 자식 컴포넌트로 지정하여 렌더링 될 수 있도록 하여 특정 경로에 리다이렉션 되는 것을 보호할 수 있도록 한다.

<br />

## RouterProvider로 라우터 전달

```bash
import React from 'react';
import ReactDOM from 'react-dom/client';
import { RouterProvider } from 'react-router-dom';
import { router } from './routes/router';

const root = ReactDOM.createRoot(document.getElementById('root') as HTMLElement);

root.render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>,
);
```

라우터 설정을 모두 완료한 후, React 애플리케이션에 라우터를 적용하기 위해 `RouterProvider`를 사용한다.

`RouterProvider` 컴포넌트는 설정된 라우터를 전체 애플리케이션에 전달하여 라우팅 기능을 활성화하는 역할을 한다.

<br />
<br />
<br />

# 마치면서

이번 포스팅에서는 `React Router Dom`을 사용해 클라이언트 사이드 라우팅을 설정하는 방법을 살펴보았다.

특히 `createBrowserRouter`를 활용한 최신 방식과 폴더 구조, `PrivateRoute`를 이용한 인증 처리 방법, 그리고 라우터를 전달하는 방법에 대해 알아보았다.

기존 `BrowserRouter` 방식은 `Routes`와 `Route` 컴포넌트를 사용해 라우팅을 정의해서 중첩된 라우트 구조로 쉽게 설정할 수 있는 반면, `createBrowserRouter`를 사용하면 복잡한 라우팅을 선언적으로 정의하여 효율적으로 관리할 수 있다는 장점이 있다.

하지만 `createBrowserRouter`를 사용하기 위해서는 새로운 접근 방식에 대한 학습이 필요하기 때문에 러닝 커브가 존재하고, 프로젝트 내에서 `react-router-dom` v6.4 이상을 사용하지 않는다면 무의미할 수 있다는 점에서 버전 의존성이 단점으로 작용할 수 있을 것 같다.

독자들은 각 프로젝트의 요구 사항에 맞는 라우팅 방식을 선택해서 보다 효율적으로 라우팅을 관리해보길 바란다.
