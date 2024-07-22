---
layout: post
title: "Storybook으로 UI 테스팅과 배포(CI) 한번에 해결하기"
category: frontend
---



<br />
<br />
<br />
# Storybook 소개

![strobook](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fc0c9b078-f570-4e92-9f4a-0ec76b4d6de7%2F%25E1%2584%2583%25E1%2585%25A1%25E1%2584%258B%25E1%2585%25AE%25E1%2586%25AB%25E1%2584%2585%25E1%2585%25A9%25E1%2584%2583%25E1%2585%25B3.png?id=9a9c29c2-ad1d-40cf-aeb1-86e254e949da&table=block)

Storybook은 **UI 테스팅 툴**로, 내부 개발자를 위한 문서화 도구와 외부 공개용 디자인 시스템의 기본 플랫폼으로 활용된다.

Storybook의 기본 구성 단위는 스토리(Story)로, 하나의 UI 컴포넌트는 여러 개의 스토리를 가질 수 있다.

각 스토리는 컴포넌트의 사용 예시를 보여주기 때문에 Storybook을 사용하면 UI 컴포넌트를 독립적으로 시각적으로 테스트하며 개발할 수 있다.

이를 통해 UI 라이브러리를 사용하는 개발자들은 코드를 보지 않고도 컴포넌트를 체험하고 활용할 수 있어 매우 유용하다.

<br />
<br />
<br />

# Storybook 설치 및 설정하기

## Storybook 설치하기

Storybook 설치를 결정했다면 `npx storybook@latest init` 명령어를 입력하면 설치할 수 있다.

프로젝트 환경에 맞게 알아서 Storybook을 설치해준다.

설치가 완료되면 root 폴더에 `.storybook` 폴더가 생성되고, 그 안에 `main.ts`와 `preview.ts` 파일이 생성된다.

![스크린샷 2024-06-27 오후 3.24.43.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F573ecb16-a880-4d85-bb93-9929df989f94%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-27_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.24.43.png?id=777fb53a-fe4f-46c8-84c3-f69ebbb71fa4&table=block)

![스크린샷 2024-06-27 오후 3.24.28.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F34a5db66-0d86-4343-8e0b-2bf64e98beaa%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-27_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.24.28.png?id=4732428d-19a6-424e-b366-7bdebf68ea1c&table=block)

또한 `src` 폴더 내부에 `stories`라는 폴더가 생성되며, 그 안에는 예시 파일들이 생성된다.

Storybook에 대한 전반적인 설정은 `.storybook` 폴더 내부에 있는 파일들에서 설정하며, 컴포넌트에 대한 story 파일 생성은 `{ComponentName}.stories.ts` 형태로 작성하면 된다.

필자는 `src` 폴더에 `stories` 폴더를 따로 분리하지 않고 `component` 폴더에 각 컴포넌트에 해당하는 폴더를 생성하여 해당 폴더 내부에서 stories 파일을 생성하는 방식으로 작성해보았다.

![스크린샷 2024-07-01 오후 2.32.59.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fedd8cfa5-ecf9-468c-9375-e58070dd5024%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_2.32.59.png?id=7960ec81-2993-4d8d-bf1b-2f21bcee7fa6&table=block)

## .storybook/main.ts & preview.ts

`.storybook` 내부에 있는 `main.ts`는 Storybook에 대한 전반적인 설정을 할 수 있는 파일이다.

해당 파일의 코드는 다음과 같은 의미를 가진다.

```tsx
// .storybook/main.ts

import type { StorybookConfig } from "@storybook/react-webpack5";

const config: StorybookConfig = {
  // Storybook에 사용한 mdx, .stories 파일의 위치를 의미합니다.
  stories: ["../src/**/*.mdx", "../src/**/*.stories.@(js|jsx|mjs|ts|tsx)"],
  // 적용할 addon을 의미합니다.
  addons: [
    "@storybook/preset-create-react-app",
    "@storybook/addon-onboarding",
    "@storybook/addon-links",
    "@storybook/addon-essentials",
    "@chromatic-com/storybook",
    "@storybook/addon-interactions",
  ],
  // 현재 Storybook의 프레임워크를 뜻합니다.
  framework: {
    name: "@storybook/react-webpack5",
    options: {},
  },
  // static 폴더의 경로를 의미합니다.
  staticDirs: ["../public"],
};
export default config;
```

추가적으로 있는 `preview.ts`는 Storybook을 실행했을 때 브라우저에서 보이는 미리보기 화면을 설정할 수 있는 파일이다.

해당 파일의 코드는 다음과 같은 의미를 가진다.

```tsx
// .storybook/preview.ts

import type { Preview } from "@storybook/react";

const preview: Preview = {
  // parameters는 스토리에 대한 메타데이터 정보들, 주로 Storybook feature와 addon에 대한 설정
  parameters: {
    actions: { argTypesRegex: "^on[A-Z].*" },
    controls: {
      matchers: {
        color: /(background|color)$/i,
        date: /Date$/,
      },
    },
  },
};

export default preview;
```

<br />
<br />
<br />

# Storybook 사용해보기

## React 컴포넌트 작성

예를 들어 Button 컴포넌트를 만든다고 가정해자.

`src/component/common` 경로에 `Button.tsx` 파일을 생성하고, `ButtonProps`를 타입으로 갖는 Button 컴포넌트를 다음과 같이 작성해보았다.

```tsx
import React, { ButtonHTMLAttributes, ReactNode } from "react";
import StyledButton from "./Button.styled";

export type ButtonTheme = "primary" | "kakao";
export type ButtonSize = "small" | "medium" | "large" | "responsive";

interface ButtonProps extends ButtonHTMLAttributes<HTMLButtonElement> {
  theme?: ButtonTheme;
  size?: ButtonSize;
  children: ReactNode;
}

export default function Button({
  theme = "primary",
  size = "medium",
  children,
  ...props
}: ButtonProps) {
  return (
    <StyledButton theme={theme} size={size} {...props}>
      {children}
    </StyledButton>
  );
}
```

현재 `theme`과 `size`를 props로 받고 있고, 특정 string 값을 입력하면 그에 따른 Button의 디자인이 변경되는 역할을 하고 있는 컴포넌트이다.

## 기본 Story 추가하기

먼저 `Meta`와 `StoryObj`에 대해 살펴보자.

`Meta`는 스토리북 메타데이터를 정의하는데 사용되고, `StoryObj`는 개별 스토리의 정의를 위해 사용된다.

### Meta Object

`meta` 객체는 Storybook이 스토리 목록에서 컴포넌트를 어떻게 표시할지를 결정하는 설정이다.

`meta` 객체를 생성하고, 생성할 Story의 컴포넌트 타입을 제네릭으로 넘겨준다.

이후 해당 컴포넌트의 세부적인 설정은 다음과 같이 작성할 수 있다.

```tsx
// Button.stories.ts

import Button, { ButtonProps } from "@/components/common/Button";
import type { Meta, StoryObj } from "@storybook/react";

const meta: Meta<ButtonProps> = {
  title: "common/Button",
  component: Button,
  tags: ["autodocs"],
  argTypes: {
    theme: {
      control: {
        type: "select",
        options: ["kakao", "outline", "black", "lightGray", "darkGray"],
      },
    },
    size: {
      control: { type: "select", options: ["small", "large", "responsive"] },
    },
  },
};

export default meta;
```

- `title`: 스토리북 UI에서 이 스토리가 어디에 위치할지를 지정한다. 여기서는 'common/Button'으로, 'common' **카테고리** 아래 'Button' 스토리가 생성되게 된다.
- `component`: 해당 메타데이터가 어떤 컴포넌트를 다루는지 지정한다. 여기서는 `Button` 컴포넌트를 의미한다.
- `tags`: 자동으로 Docs를 생성하기 위해 사용되는 태그이다.
- `argTypes`: 스토리북에서 해당 컴포넌트의 props를 조절할 수 있는 UI 컨트롤을 정의할 수 있다. 현재 Button 컴포넌트에서는 `theme`과 `size` 속성에 대해 `select` 컨트롤 타입을 설정하여 조절할 수 있도록 하였다.

최종적으로 옵션을 설정하였다면, `meta` 객체를 export defalt 해준다.

### StoryObj

메타데이터를 설정하였다면 개별 스토리의 정의를 위해 `StoryObj`를 통해 Story의 컴포넌트 타입을 정의한다.

이후 각 스토리를 작성합니다.

예를 들어 `kakao` theme을 갖는 `Kakao` 스토리와 `primary` theme을 갖는 `Primary` 스토리를 정의해본다고 가정한다면 다음과 같이 작성할 수 있다.

```tsx
// Button.stories.ts

...

type Story = StoryObj<ButtonProps>;

export const Kakao: Story = {
  args: {
    children: '버튼 텍스트',
    theme: 'kakao',
    size: 'large',
  },
};

export const Primary: Story = {
  args: {
    children: '버튼 텍스트',
    theme: 'primary',
    size: 'large',
  },
};
```

- `StoryObj`를 통해 컴포넌트 타입을 정의한다.
- 각 스토리를 `export`하고 `Story` 타입으로 설정하고 객체를 생성한다. 해당 스토리 객체 내부에서 `args` 속성을 통해 해당 Button 컴포넌트의 props에 따른 기본 값을 설정할 수 있다.

### 최종 코드

`meta`와 `StoryObj`를 통한 스토리북 생성을 위한 최종 코드는 다음과 같다.

```tsx
// Button.stories.ts

import Button, { ButtonProps } from "@/components/common/Button";
import type { Meta, StoryObj } from "@storybook/react";

const meta: Meta<ButtonProps> = {
  title: "common/Button",
  component: Button,
  tags: ["autodocs"],
  argTypes: {
    theme: {
      control: {
        type: "select",
        options: ["kakao", "outline", "black", "lightGray", "darkGray"],
      },
    },
    size: {
      control: { type: "select", options: ["small", "large", "responsive"] },
    },
  },
};

export default meta;

type Story = StoryObj<ButtonProps>;

export const Kakao: Story = {
  args: {
    children: "버튼 텍스트",
    theme: "kakao",
    size: "large",
  },
};

export const Primary: Story = {
  args: {
    children: "버튼 텍스트",
    theme: "primary",
    size: "large",
  },
};
```

## hooks를 곁들인 Story 추가하기

hooks와 같이 컴포넌트가 동작하는 Story를 추가하려면 렌더링할 컴포넌트와 hooks와 같이 함수로 정의하고 render시키면 된다.

사용자가 선택한 카테고리를 상태를 갖는 Wish라는 컴포넌트를 예시를 들어 설명해보면 다음과 같이 작성해볼 수 있다.

```tsx
import React from 'react';
import { Meta, StoryObj } from '@storybook/react';
import useFilter from '@hooks/useFilter';
import Wish, { WishProps } from '.';

// meta 생성

...

**function WishWithFilterHooks(args: WishProps) {
  const { selectedWish, selectWish } = useFilter('', FILTER_WISHS[0].wish);

  return <Wish {...args} selectedWish={selectedWish} selectWish={selectWish} />;
}**

export const Default: Story = {
  **render: (args) => <WishWithFilterHooks {...args} />,**
  args: {
    selectedWish: FILTER_WISHS[0].wish,
    selectWish: () => {},
  },
};
```

WishWithFilterHooks라는 함수를 정의하고 useFilter라는 커스텀훅을 불러온다.

이후 Wish 컴포넌트에 해당 hooks와 관련한 상태를 props로 전달해주어 state에 따라 동작할 수 있도록 하는 컴포넌트 함수를 만든다.

주의할 점은 jsx문법을 사용하기 때문에 파일 확장자또한 tsx 혹은 jsx로 작성해야한다.

이후 Story에 render 속성을 통해 해당 컴포넌트를 렌더링 시키면 hooks와 함께하는 Story를 추가할 수 있다.

### 결과

![Jul-03-2024 22-06-15.gif](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fa9565c80-8a2c-4780-b09c-b061ff8bb608%2FJul-03-2024_22-06-15.gif?id=c3b64706-76f1-4b49-8165-85dd3807324c&table=block)

## Preview 확인하기

`npm run storybook` 명령어를 입력하면 브라우저에 스토리북이 실행되며, 다음과 같이 common카테고리에 Button컴포넌트에 해당하는 Kakao, Primary스토리가 생성된 것을 확인할 수 있다.

추가로, 메타 데이터에 `tags: ['autodocs']`를 설정했기 때문에 자동으로 Docs가 생성된 것도 확인할 수 있다.

또한 각 생성한 Story도 잘 나오는 것을 확인할 수 있다.

![결과](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fd6cd56c9-1553-4970-9835-c82c073a42f1%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_2.26.19.png?id=503f60e8-01ac-49e0-b8f8-c6d717abcdd3&table=block)

## `useParams` 모킹 및 React Router 컴포넌트용 Storybook 설정하기

React 애플리케이션을 개발할 때 URL 매개변수(`useParams`)에 의존하는 컴포넌트를 테스트할 필요가 있다.

이러한 경우 Storybook에서 `useParams`를 모킹(mocking)하는 것이 유용하다.

### 컴포넌트 설정

먼저 `useParams`를 사용하는 간단한 컴포넌트를 만들어보자.

이 컴포넌트는 URL에서 `themeKey`를 가져와 표시한다고 가정한다.

```tsx
// SectionHeader.tsx
import React from "react";
import { useParams } from "react-router-dom";

const SectionHeader: React.FC = () => {
  const { themeKey } = useParams<{ themeKey: string }>();

  return <h1>Theme: {themeKey}</h1>;
};

export default SectionHeader;
```

### Storybook 설정

이제 Storybook에서 `useParams`를 모킹하기 위해 필요한 설정을 해본다.

먼저, `useParams`를 모킹할 데코레이터를 만든다.

이 데코레이터는 Storybook 스토리를 `MemoryRouter`로 감싸고 초기 URL을 설정한다.

```tsx
// SectionHeader.stories.tsx

import React, { ReactNode } from "react";
import { MemoryRouter, Route, Routes } from "react-router-dom";
import { Meta, StoryObj } from "@storybook/react";
import SectionHeader from "./SectionHeader";

interface MockUseParamsDecoratorProps {
  themeKey: string;
  children: ReactNode;
}

function MockUseParamsDecorator({
  themeKey,
  children,
}: MockUseParamsDecoratorProps) {
  return (
    <MemoryRouter initialEntries={[`/theme/${themeKey}`]}>
      <Routes>
        <Route path="/theme/:themeKey" element={children} />
      </Routes>
    </MemoryRouter>
  );
}
```

### Storybook 메타 설정

이제 `MockUseParamsDecorator`를 사용하여 Storybook 메타 설정한다.

```tsx
...

const meta: Meta<typeof SectionHeader> = {
  title: 'common/SectionHeader/Default',
  component: SectionHeader,
  tags: ['autodocs'],
  **decorators: [
    (Story) => (
      <MockUseParamsDecorator themeKey="light-gifts">
        <Story />
      </MockUseParamsDecorator>
    ),
  ],**
};

export default meta;

type Story = StoryObj<typeof SectionHeader>;

export const Default: Story = {};
```

이 설정을 통해 `SectionHeader` 컴포넌트는 `themeKey`가 "light-gifts"인 URL로 렌더링하게 된다.

<br />
<br />
<br />

# Storybook 절대경로 설정

![스크린샷 2024-06-27 오후 9.53.37.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F598b5c49-7b46-4b16-b2a6-8df22c980fe3%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-27_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.53.37.png?id=482720fe-061b-400d-9bc9-031c989c82b2&table=block)

Button 컴포넌트의 스토리를 작성하고 스토리북을 실행해본 결과 Button 컴포넌트를 찾을 수 없다고 나왔다

컴포넌트의 경로를 상대 경로로 변경한 후 스토리북을 실행해본 결과가 잘 나오는 것을 확인할 수 있었고, 이는 절대 경로 설정으로 인해 생긴 이슈로 판단하게 되었다.

Storybook에서의 절대 경로 이슈를 구글링해본 결과 `tsconfig-paths-webpack-plugin`을 설치하고 스토리북을 설정하면 해결할 수 있다는 것을 알게되었다.

이후 아래의 공식 문서를 통해 해당 플러그인에 대해 검색하여 자세히 확인해보았다.

[Storybook](https://storybook.js.org/docs/builders/webpack#typescript-modules-are-not-resolved-within-storybook)

![스크린샷 2024-06-27 오후 10.21.44.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F245ce781-340a-4e4b-b4e7-a8a0509413e1%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-27_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_10.21.44.png?id=57019eca-6808-4557-91ce-df5be0711f1f&table=block)

위 공식 문서를 토대로 `tsconfig-paths-webpack-plugin`을 설치하기 위해 `npm i -D tsconfig-paths-webpack-plugin` 명령어를 입력한 후, `.storybook/main.ts` 파일에 webpackFinal을 다음과 같이 추가했다.

```tsx
import type { StorybookConfig } from "@storybook/react-webpack5";
import TsconfigPathsPlugin from "tsconfig-paths-webpack-plugin";

const config: StorybookConfig = {
  stories: ["../src/**/*.mdx", "../src/**/*.stories.@(js|jsx|mjs|ts|tsx)"],
  addons: [
    "@storybook/preset-create-react-app",
    "@storybook/addon-onboarding",
    "@storybook/addon-links",
    "@storybook/addon-essentials",
    "@chromatic-com/storybook",
    "@storybook/addon-interactions",
  ],
  framework: {
    name: "@storybook/react-webpack5",
    options: {},
  },
  staticDirs: ["../public"],
  webpackFinal: async (config) => {
    if (config.resolve) {
      config.resolve.plugins = [
        ...(config.resolve.plugins || []),
        new TsconfigPathsPlugin({
          extensions: config.resolve.extensions,
        }),
      ];
    }
    return config;
  },
};
export default config;
```

위와 같이 입력하고 다시 확인해본 결과 절대 경로 이슈가 해결된 것을 확인할 수 있었다.

![결과](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F0ad7b380-4e04-48f0-921e-9c825d9b23ff%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-27_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.53.20.png?id=88cd9403-69fa-4bdb-a0bc-6bf2511fe024&table=block)

<br />
<br />
<br />

# Storybook 온라인 배포하기

## 정적 앱으로 내보내기

스토리북을 정적 앱으로 배포하기 위해 `npm run build-storybook` 명령어를 실행하면 `storybook-static` 디렉토리에 정적인 스토리북이 생성되며, 이를 정적 사이트 호스팅 서비스에 배포할 수 있다.

## 크로마틱(Chromatic)으로 배포하기

스토리북 관리자가 만든 무료 배포 서비스인 [Chromatic](https://www.chromatic.com/?utm_source=storybook_website&utm_medium=link&utm_campaign=storybook)을 사용하여 더욱 쉽게 배포할 수 있다.

Chromatic을 사용하여 클라우드에서 스토리북을 안전하게 배포하고 호스팅하기 위해 설치를 진행해보자.

### Chromatic 설치

`npm install -D chromatic` 명령어를 통해 chromatic을 설치한다.

이후 [Chromatic](https://www.chromatic.com/start?utm_source=storybook_website&utm_medium=link&utm_campaign=storybook) 사이트에 들어가서 GitHub 계정으로 로그인하고 연결할 repository를 설정한다.

Storybook을 클릭하고 프로젝트를 위해 생성된 고유한 `project-token`을 복사한다.

![스크린샷 2024-07-01 오후 3.28.55.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Ff5d391b2-480a-41dd-bfe2-5ab4fe25791e%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.28.55.png?id=addeabe2-1c5b-480c-b6ac-3e044c316f4f&table=block)

![스크린샷 2024-07-01 오후 3.29.09.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F502f3e98-769f-4ae3-8bdc-e49deb31aec2%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.29.09.png?id=a37b5bcd-c37c-4be7-87a9-c76886f4b793&table=block)

![스크린샷 2024-07-01 오후 3.29.34.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fa4b9d559-41c9-4575-a6b2-add7a0d0d3ff%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.29.34.png?id=8b394510-3780-40cc-bca3-e252828fe3ff&table=block)

![스크린샷 2024-07-01 오후 3.30.08.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F3c9f52ea-c4ca-4a57-81aa-6529fff6ea28%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.30.08.png?id=00f6dad2-5072-40a6-a828-455a91e6f1e8&table=block)

### 명령어를 통한 배포

명령어를 통해 스토리북을 빌드(build)하고 배포하기 위해 `npx chromatic --project-token={project-token}` 명령어를 실행한다.

![명령어 배포](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fb2b13388-079f-4831-b87f-acee7b0d49c6%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.44.48.png?id=9805a7bc-1289-491f-abad-76c8c27a3be0&table=block)

완료되면 배포된 스토리북의 `https://random-uuid.chromatic.com` 링크를 받을 수 있다. 해당 링크를 팀과 공유하여 피드백을 받으면 된다.

### 크로마틱을 통한 지속적 배포(CI)하기

이제 프로젝트가 GitHub 저장소에 호스팅 되었으므로 자동으로 스토리북을 배포하기 위해 지속적 통합(continuous integration, CI) 서비스를 이용할 수 있다.

[GitHub Actions](https://github.com/features/actions)는 GitHub에 내장된 무료 CI 서비스로, 쉽게 자동으로 배포할 수 있도록 해준다.

### GitHub Actions 추가하기

![스크린샷 2024-07-01 오후 4.14.29.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F32539ab7-2d8e-4b87-ac5e-7a3e3f798e6e%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.14.29.png?id=89418012-6cec-4af8-81f0-b61bd80eaabc&table=block)

![스크린샷 2024-07-01 오후 5.22.52.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fc55e96d2-a3a4-4232-9300-0e65352ab118%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_5.22.52.png?id=fa02786c-839a-4431-b755-a7e8a4906705&table=block)

CI로 지속적 배포를 할 것이기 때문에 package.json의 scripts 부분에서 chromatic 명령어를 제거하고, 프로젝트의 기본 폴더에 `.github` 디렉토리를 만들고 그 안에 `workflows` 디렉토리를 만들어준다.

이후 내부에 `chromatic.yml`이라는 파일을 아래와 같이 생성한다.

```yaml
# Workflow name
name: "Chromatic Deployment"

# Event for the workflow
on: push

# List of jobs
jobs:
  test:
    # Operating System
    runs-on: ubuntu-latest
    # Job steps
    steps:
      - uses: actions/checkout@v1
      #👇 Adds Chromatic as a step in the workflow
      - uses: chromaui/action@v1
        # Options required for Chromatic's GitHub Action
        with:
          #👇 Chromatic projectToken, see https://storybook.js.org/tutorials/intro-to-storybook/react/ko/deploy/ to obtain it
          projectToken: ${{ secrets.CHROMATIC_PROJECT_TOKEN }}
          token: ${{ secrets.GITHUB_TOKEN }} // 삭제는 선택!
```

`CHROMATIC_PROJECT_TOKEN`을 코드에 기재하면 보안에 취약하기 때문에 따로 GitHub Action의 `secret`을 통해 관리해야 한다.

여기서 `GITHUB_TOKEN`은 사용해도 되고 안 해도 된다.

.yml 작성이 완료되면 이어서 commit과 push를 하기 전에 `CHROMATIC_PROJECT_TOKEN`과`GITHUB_TOKEN(선택)`을 secret으로 설정해보자.

![스크린샷 2024-07-01 오후 4.35.19.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F486a0513-267c-4a45-9696-ee74b82265cd%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.35.19.png?id=f550be64-98d6-4ed3-b083-435d968fe924&table=block)

![스크린샷 2024-07-01 오후 4.36.28.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F366d051d-e08c-4787-bb21-6d02574ac7e8%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.36.28.png?id=09309de8-c581-40f2-9c42-959612ab711c&table=block)

위와 같이 먼저 해당 repository에 Settings > Secrets and Variables > Actions > Respository secrets에서 New repository secret버튼을 클릭하고 Storybook을 배포하면서 받은 토큰을 `CHROMATIC_PROJECT_TOKEN`에 값으로 추가한다.

만약, `GITHUB_TOKEN`도 추가할 경우, 개인 설정을 통해 Personal access token을 발급받아야 한다.

![스크린샷 2024-07-01 오후 4.39.00.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F6555ea19-6e88-4bb6-b3a9-1cf756110498%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.39.00.png?id=facce4cc-b943-46dd-b348-00dc2b302988&table=block)

![스크린샷 2024-07-01 오후 4.40.00.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fbd08c28c-e3ab-447f-b6a9-86c4efcd2d37%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.40.00.png?id=eea519b8-bf96-4ad8-b119-e08fa0f4d08a&table=block)

![스크린샷 2024-07-01 오후 4.41.00.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F68ab7a49-3490-4fa0-84ab-fd036935e1c7%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.41.00.png?id=cc86a84c-1cc6-43bf-95fd-85037b7b9b3f&table=block)

![스크린샷 2024-07-01 오후 4.41.49.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F9cc93d11-6954-46e1-8708-30cfcfe19af6%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.41.49.png?id=8966db00-270b-4a0b-b0bd-2689ce06ec0d&table=block)

![스크린샷 2024-07-01 오후 4.42.25.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F1fefa50b-bde5-4fe4-9548-3d8a5f74762f%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.42.25.png?id=917ee94a-4662-49eb-ad1f-20c613a3a28a&table=block)

![스크린샷 2024-07-01 오후 4.43.20.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F81949adf-e08b-44ae-96b1-9f0e23f7360f%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.43.20.png?id=35660ee8-a008-4bfb-ab63-112009dbbb52&table=block)

쓰는 목적을 Note에 작성하고 scope은 이단 repo로 설정하였다.

이후 **Generate Token** 버튼을 클릭하여 token을 생성한다.

생성한 token은 다른 곳에 잘 적어놓는다.

![스크린샷 2024-07-01 오후 4.45.31.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fcd54a582-3bca-4e55-a65b-6dec186c4353%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.45.31.png?id=a951ce51-88c1-4c62-8419-d78b6b7bdb7e&table=block)

이후 secret에 **GITHUB_TOKEN**이라는 이름으로 secret을 생성하려고 했는데 **“GITHUB\_”**라는 이름으로 시작할 수 없다고 하여, `TOKEN`이라는 이름으로 다시 secret을 생성하였다.

![스크린샷 2024-07-01 오후 4.46.27.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F1f80a2c5-b840-4f3c-b5b2-236d7710d76a%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.46.27.png?id=968c3eae-f10c-467c-b342-6a2a5601f39a&table=block)

성공적으로 secret을 추가하고, yml 파일에서 `GITHUB_TOKEN` 대신 `TOKEN`을 사용하도록 수정한다.

```tsx
# Workflow name
name: 'Chromatic Deployment'

# Event for the workflow
on: push

# List of jobs
jobs:
  test:
    # Operating System
    runs-on: ubuntu-latest
    # Job steps
    steps:
      - uses: actions/checkout@v1
      - run: yarn
        #👇 Adds Chromatic as a step in the workflow
      - uses: chromaui/action@v1
        # Options required for Chromatic's GitHub Action
        with:
          #👇 Chromatic projectToken, see https://storybook.js.org/tutorials/intro-to-storybook/react/ko/deploy/ to obtain it
          projectToken: ${{ secrets.CHROMATIC_PROJECT_TOKEN }}
          # token: ${{ secrets.TOKEN }}
```

수정이 완료되면 commit하고 push한다.

이후 repository의 actions 탭에서 CI 작동 여부를 확인해본다.

![스크린샷 2024-07-01 오후 5.26.33.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F71f24472-4284-4c8b-8c8b-10ace0635c27%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-01_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_5.26.33.png?id=d49643c5-4599-40b4-bedd-7016f9047b04&table=block)

잘 작동하는 것을 확인할 수 있다.

### + 스토리북 배포 링크, PR 코멘트 자동으로 남기는 액션 추가

![다운로드 (1).png](<![<https://prod-files-secure.s3.us-west-2.amazonaws.com/13897cab-0dd6-431f-b847-04477372a586/31ac7e4b-41dc-41a9-a33d-c0da53661488/%E1%84%83%E1%85%A1%E1%84%8B%E1%85%AE%E1%86%AB%E1%84%85%E1%85%A9%E1%84%83%E1%85%B3_(1).png>](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F31ac7e4b-41dc-41a9-a33d-c0da53661488%2F%25E1%2584%2583%25E1%2585%25A1%25E1%2584%258B%25E1%2585%25AE%25E1%2586%25AB%25E1%2584%2585%25E1%2585%25A9%25E1%2584%2583%25E1%2585%25B3_(1).png?id=2e4c949f-1647-41e4-a71e-56c4488ff388&table=block)>)

다음과 같이 배포 링크를 PR에 코멘트로 자동으로 남기도록 설정할 수 있다.

```tsx
# Workflow name
name: 'Chromatic Deployment'

# Event for the workflow
on: push

# List of jobs
jobs:
  test:
    # Operating System
    runs-on: ubuntu-latest
    # Job steps
    steps:
      ...
      - name: Create comment PR
        uses: thollander/actions-comment-pull-request@v1
        env:
          TOKEN: ${{ secrets.TOKEN }}
        with:
          message: "🚀storybook: ${{ steps.chromatic.outputs.storybookUrl }}"
```

<br />
<br />
<br />

# 마치면서

이번 글에서는 Storybook을 설치하고 설정하는 방법을 다뤘보았다.

Storybook을 통해 UI 컴포넌트를 독립적으로 개발하고 테스트하는 방법을 배우고, Chromatic을 사용하여 스토리북을 배포하고 CI/CD 파이프라인에 통합하는 방법을 알게되었다.

이러한 과정을 통해 UI 컴포넌트를 더 효율적으로 개발하고, 팀 내에서 공유하고 피드백을 받을 수 있어, 특히 디자이너와의 협업에서의 큰 도움이 될 것 같다는 생각이 들었다.
