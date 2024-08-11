---
layout: post
title: "React Context API로 전역 상태 관리와 사용자 인증 구현해보기"
category: frontend
---

<br />
<br />
<br />

# ContextAPI란?

<img src="https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F66b40315-e83d-4b7a-b349-13e02f338e40%2Fimage.png?id=a78b3c22-37bf-4379-a6f8-9b3802f18273&table=block" width="400px">

<br />

**Context API**는 React에서 전역적인 상태를 관리하고, 계층 구조를 통해 데이터나 기능을 전달하는 데 사용되는 도구이다.

대부분 컴포넌트 트리에서 데이터를 전달할 때는 props를 사용하지만, 깊이 중첩된 컴포넌트 트리에서는 props를 계속 전달해야 하는 문제가 발생할 수 있다. 이를 우리는 **Props Drilling**이라고 한다.

이러한 **Props Drilling**의 문제를 Context API가 해결해줄 수 있고, 특히 전역 상태 관리가 필요한 경우 유용하게 사용할 수 있다.

<br />
<br />
<br />

# createContext, Provider, useContext의 역할

Context API를 사용할 때 주로 사용하는 세 가지 주요 구성 요소는 `createContext`, `Provider`, `useContext` 가 있다.

각각의 역할은 다음과 같다.

<br />

## **createContext**

![스크린샷 2024-08-11 오후 3.32.26.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F9c2b9662-25df-443d-a0b4-6a39fa9c798c%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-08-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.32.26.png?id=85ff090c-a593-4238-9bf0-49d20d14aa9a&table=block)

<br />

`createContext`는 새로운 Context 객체를 생성한다.

생성된 Context 객체는 이후에 데이터를 공급하고 받아오는 데 사용된다.

<br />

## **Provider**

![스크린샷 2024-08-11 오후 3.32.43.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F3ffa2368-bbb5-40ed-8c51-7be9ba69d676%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-08-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.32.43.png?id=eda4e8ec-d942-4b85-a917-6e06fd036086&table=block)

<br />

`Provider`는 Context에서 값을 공급하는 역할을 한다.

`Provider` 컴포넌트는 계층 구조에서 하위 컴포넌트들에게 데이터를 전달하고 이 데이터는 Context의 `value` 속성을 통해 설정된다.

<br />

## **useContext**

![스크린샷 2024-08-11 오후 3.36.18.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F7d4062f0-cfda-4496-9421-95a3b8698a93%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-08-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.36.18.png?id=124102aa-13f4-4bdf-8665-c309cc50149b&table=block)

<br />

`useContext`는 Context에 저장된 값을 가져오기 위해 사용되는 훅이다.

해당 훅을 사용하면, Context의 값을 접근할 수 있다.

<br />
<br />
<br />

# ContextAPI 사용해보기

이제 Context API를 사용하여 사용자 인증 시스템을 구현해보자.

해당 예시에서는 `AuthContext`를 생성하고, 사용자가 로그인 또는 로그아웃할 때 Context를 통해 상태를 관리할 수 있도록 구현해보려고한다.

<br />

## 폴더구조

```bash
context
└── auth
     ├── AuthContexsx
     ├── AuthProvider.tsx
     └── useAuth.ts
```

먼저 프로젝트의 폴더 구조를 위와같이 정의해보았다.

사용자 인증 시스템과 관련한 Context 파일들은 `context/auth` 디렉토리 아래에 모아 관리하도록 하였다.

<br />

## `AuthContext` 작성 해보기

`AuthContext`는 사용자 인증 상태와 관련된 데이터를 제공하는 Context 객체이다.

우리는 `createContext`를 사용하여 Context 객체를 생성할 수 있다.

```tsx
import { createContext } from "react";

interface AuthContextProps {
  isAuthenticated: boolean;
  login: (email: string) => void;
  logout: () => void;
}

const AuthContext = createContext<AuthContextProps | undefined>(undefined);

export default AuthContext;
```

해당 코드에서는 `AuthContextProps` 인터페이스를 정의하여 인증 상태(`isAuthenticated`), 로그인 함수(`login`), 로그아웃 함수(`logout`)를 포함한 Context를 생성하였다.

<br />

## `AuthProvider` 작성 해보기

```tsx
import React, {
  ReactNode,
  useEffect,
  useState,
  useMemo,
  useCallback,
} from "react";
import { useNavigate } from "react-router-dom";
import { useSessionStorage } from "@hooks/useSessionStorage";
import { ROUTE_PATH } from "@routes/path";
import AuthContext from "./AuthContext";

interface AuthProviderProps {
  children: ReactNode;
}

export default function AuthProvider({ children }: AuthProviderProps) {
  const navigate = useNavigate();
  const [authToken, setAuthToken] = useSessionStorage("authToken", "");
  const [isAuthenticated, setIsAuthenticated] = useState(!!authToken);

  useEffect(() => {
    setIsAuthenticated(!!authToken);
  }, [authToken]);

  const login = useCallback(
    (email: string) => {
      setAuthToken(userName);
    },
    [setAuthToken]
  );

  const logout = useCallback(() => {
    setAuthToken("");
    navigate(ROUTE_PATH.HOME);
  }, [setAuthToken, navigate]);

  const value = useMemo(
    () => ({
      isAuthenticated,
      login,
      logout,
    }),
    [isAuthenticated, login, logout]
  );

  return <AuthContext.Provider value={value}>{children}</AuthContext.Provider>;
}
```

`AuthProvider`는 `AuthContext`의 `Provider` 역할을 하고 사용자 인증 상태와 관련된 데이터를 관리하고 제공한다.

먼저 로그인 시, `useSessionStorage` 훅을 사용해 세션 스토리지에 사용자의 이메일(`email`)을 `authToken`이라는 키로 저장하도록 구현하였다.

로그아웃 시에는 `authToken`을 빈 값으로 설정하고, 사용자를 메인 페이지로 리다이렉션하도록 구현하였다.

또한, `useEffect`를 통해 `authToken`의 변경을 감지하여 `isAuthenticated` 상태를 자동으로 업데이트하도록 구현해보았다.

최종적으로, 사용자 인증 여부(`isAuthenticated`), 로그인(`login`), 로그아웃(`logout`) 메소드를 `value`로 설정해 `Provider`를 통해 Context의 값을 자식 컴포넌트에 전달하도록 한다.

이를 통해 `useAuth` 훅을 사용하여 다른 컴포넌트에서 전역적으로 해당 상태와 메소드에 쉽게 접근할 수 있게 된다.

<br />

## `useAuth` 작성 해보기

`useAuth` 훅은 `useContext`를 사용해 `AuthContext`에 접근할 수 있도록 해준다.

해당 훅을 사용해 다른 컴포넌트에서 쉽게 인증 상태를 가져오거나, 로그인 및 로그아웃 함수를 호출할 수 있다.

```tsx
import { useContext } from "react";
import AuthContext from "./AuthContext";

const ERROR_MESSAGE = "useAuth must be used within an AuthProvider";

export const useAuth = () => {
  const context = useContext(AuthContext);

  if (!context) throw new Error(ERROR_MESSAGE);
  return context;
};
```

만약, `AuthContext`가 `Provider`로 래핑되어있지 않은 경우 오류를 발생시켜, 잘못된 사용을 방지할 수 있도록 한다.

<br />

## Provider 래핑하기

```tsx
import { Outlet } from "react-router-dom";
import AuthProvider from "@context/auth/AuthProvider";

function App() {
  return (
    <AuthProvider>
      <Outlet />
    </AuthProvider>
  );
}

export default App;
```

이제 애플리케이션의 최상위 컴포넌트에서 `AuthProvider`를 사용하여 전체 애플리케이션을 감싸준다.

이렇게 하면 `AuthContext`를 통해 인증 상태를 전역적으로 관리할 수 있습니다.

<br />

## `useAuth` 사용하기

이제 사용자 인증 시스템을 전역 상태로 구현한 Context에 접근하기 위해 `useAuth` 훅을 사용해보자.

로그인 폼과 로그아웃 기능은 다음과 같이 구현해볼 수 있다.

<br />

### `AuthForm` 컴포넌트로부터 사용자 정보 받기

```tsx
...
import { InputField } from '@components/common';

export interface AuthFormProps {
  userInfo: {
    email: string;
    password: string;
  };
  onChange: (e: ChangeEvent<HTMLInputElement>) => void;
  submitButton: ReactNode;
  isSignUp?: ReactNode;
}

export default function AuthForm({ userInfo, onChange, submitButton, isSignUp }: AuthFormProps) {
  return (
    <FormContainer>
      <InputContainer>
        <InputField
          label="이메일"
          labelFor="email"
          placeholder="이메일"
          type="text"
          name="email"
          size="large"
          onChange={onChange}
          value={userInfo.email}
          srOnly
        />
      </InputContainer>
      <InputContainer>
        <InputField
          label="비밀번호"
          labelFor="password"
          placeholder="비밀번호"
          type="password"
          name="password"
          size="large"
          onChange={onChange}
          value={userInfo.password}
          srOnly
        />
      </InputContainer>
      <ButtonContainer>{submitButton}</ButtonContainer>
      {isSignUp}
    </FormContainer>
  );
}
```

위와 같이 사용자로부터 이메일과 비밀번호를 입력받는 `AuthForm` 컴포넌트를 구현하였다.

이제 사용자로부터 입력받은 정보를 기반으로 `useAuthForm`이라는 커스텀 훅을 통해 사용자 입력 정보를 상태로 관리하고 인증하는 과정까지 구현해보자.

<br />

### `useAuthForm` 커스텀 훅은 통한 로그인 진행하기

`AuthForm` 컴포넌트로부터 사용자가 입력한 정보를 기반으로 로그인할 수 있도록 커스텀 훅을 다음과 같이 구현해보았다.

```tsx
import { useAuth } from "@context/auth/useAuth";
import { ROUTE_PATH } from "@routes/path";
import { ChangeEvent, FormEvent, useState } from "react";
import { useNavigate } from "react-router-dom";

interface UserInfoState {
  email: string;
  password: string;
}

export default function useAuthForm() {
  const navigate = useNavigate();
  const { login } = useAuth();
  const [userInfo, setUserInfo] = useState<UserInfoState>({
    email: "",
    password: "",
  });

  const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setUserInfo({ ...userInfo, [name]: value });
  };

  const handleLogin = () => {
    if (userInfo.email !== "" && userInfo.password !== "") {
      login(userInfo.email);
      navigate(ROUTE_PATH.HOME);
    }
  };

  const handleSubmit = (e: FormEvent) => {
    e.preventDefault();
    handleLogin();
  };

  return {
    userInfo,
    handleChange,
    handleSubmit,
  };
}
```

먼저 `useAuth`를 사용하여 Context API의 `login` 함수를 가져온다.

이후에 사용자가 입력한 이메일과 비밀번호를 `userInfo`라는 상태로 관리하여, 입력값이 변경될 때마다 `handleChange` 함수가 호출되어 상태가 업데이트되도록한다.

최종적으로, 사용자가 이메일과 비밀번호를 모두 입력하고 제출 버튼을 클릭하면 `handleLogin` 함수가 호출되고, `login` 함수를 통해 사용자 인증을 진행한다.

추가적으로, 인증에 성공하면 홈 화면으로 리다이렉션도록 구현하였다.

<br />

### 마이페이지에서 로그아웃하기

로그아웃 기능 역시 `useAuth`를 사용해 간단하게 구현할 수 있다.

```tsx
...
import { useAuth } from '@context/auth/useAuth';
import { useSessionStorage } from '@hooks/useSessionStorage';

export default function AccountOverview() {
  const { logout } = useAuth();
  const [userName] = useSessionStorage('authToken', '');

  return (
    <MyAccountContainer>
      <Greeting>{userName}님 안녕하세요!</Greeting>
      <ButtonContainer>
        <Button size="small" theme="darkGray" onClick={logout}>
          로그아웃
        </Button>
      </ButtonContainer>
    </MyAccountContainer>
  );
}
```

필자는 `AccountoverView` 컴포넌트를 통해 사용자의 정보를 나타내고 로그아웃을 진행할 수 있도록 하는 컴포넌트를 만들었다.

먼저 `useAuth`를 통해 `logout` 함수를 가져와 사용자가 로그아웃할 수 있도록 한다.

이후, 사용자가 로그아웃 버튼을 클릭하면 `logout` 함수가 호출되어 세션 스토리지에서 `authToken`에 빈 스트링 값이 저장되고, 사용자는 다시 로그인 화면으로 리디렉션된다.

<br />
<br />
<br />

# Context API의 한계?

**Context API**는 React에 내장된 기능이기 때문에 별도의 설치나 설정이 필요하지 않아 간단하게 전역 상태 관리에는 유용하다.

그러나, 몇 가지 한계점이 존재한다.

<br />

## **1. 렌더링 이슈**

![스크린샷 2024-08-11 오후 3.34.08.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F6569d27c-f911-439e-9db2-9c992d698fa9%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-08-11_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.34.08.png?id=00e4258e-2b4e-4e76-a01a-0eb7587d4c65&table=block)

Context API를 사용하면 `Provider`에 설정된 값이 변경될 때마다 해당 값을 사용하는 모든 하위 컴포넌트가 리렌더링된다.

예를 들어, `MyContextProvider`가 매번 새로운 값을 제공하는 경우, 그 값이 새로운 참조일 때마다 관련된 모든 컴포넌트가 불필요하게 리렌더링된다.

만약, 대규모 애플리케이션에서는 이 문제는 더욱 심각해질 수 있다.

<br />

## **2. 규모가 큰 애플리케이션에서의 관리 어려움**

Context API는 작은 규모의 프로젝트나 단순한 상태 관리에는 적합하지만, 상태 관리가 복잡해지거나 애플리케이션의 규모가 커지면 관리가 어려워질 수 있다.

여러 개의 Context를 만들어 상태를 분리할 수 있지만, 이러한 접근 방식은 오히려 복잡도를 증가시키고, 코드의 가독성을 저하시킬 수 있다.

<br />
<br />

결론적으로, Context API는 간단한 전역 상태 관리에 매우 유용하지만, 대규모 프로젝트에서는 성능 문제나 관리의 복잡성이 증가할 수 있다.

따라서, 프로젝트의 요구 사항에 따라 Context API를 사용할지, 또는 더 강력한 전역 상태 관리 도구를 사용할지를 판단해서 사용해야한다는 점을 유의하자.

<br />
<br />
<br />

# 마치면서

이번에는 **React Context API**를 사용해 사용자 인증 시스템을 구현하는 방법을 살펴보았다.

기본적으로 Context API를 사용하면 전역적인 상태 관리가 간편해지고, 특히 인증과 같은 반복적으로 사용되는 기능을 손쉽게 관리할 수 있다는 것을 알 수 있었다.

또한, `createContext`, `Provider`, `useContext`의 역할을 이해하고, 이들을 활용해서 사용자 인증, 로그인, 로그아웃 등의 기능을 구현하는 과정을 단계별로 알아보았다.

물론, Context API에는 몇 가지 한계가 있지만, 프로젝트의 규모와 요구 사항에 맞게 적절히 사용하면 충분히 유용한 도구가 될 수 있다는 것 또한 알게 되었다.

<br />

독자분들도 진행 중인 프로젝트에 맞게 다양한 상태 관리 도구를 평가해보고, Context API가 적합하다면 이를 활용해 애플리케이션의 상태 관리를 보다 효율적으로 해보길 바란다.
