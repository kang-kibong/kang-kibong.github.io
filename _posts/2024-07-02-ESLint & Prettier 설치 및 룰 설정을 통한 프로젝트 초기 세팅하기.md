---
layout: post
title: "ESLint & Prettier 설치 및 룰 설정을 통한 프로젝트 초기 세팅하기"
category: frontend
---

<br />
<br />
<br />
# Prettier

![prettier](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F3ee51dc3-7337-4ff7-be1f-78f1cda77386%2Fimage.jpg?id=4c55162c-a04f-4c31-b83f-9559a5a579f2&table=block)

Prettier는 코드 정리 규칙을 세부적으로 설정해놓으면, 정해진 **규칙**에 맞게 자동으로 정렬해서 가독성을 높이고 **코드 스타일**을 통일할 수 있는 플러그인이다.

## Prettier 설치하기

`npm install -D prettier` 를 통해 prettier를 개발자 모드로 설치한다.

## Prettier 옵션들 톺아보기

```typescript
{
  "arrowParens": "always", // 화살표 함수의 매개변수가 하나일 때 괄호를 사용할지 여부
  "bracketSpacing": true, // 객체 리터럴에서 중괄호 내부에 공백 삽입 여부
  "endOfLine": "auto", // EoF 방식, OS별로 처리 방식이 다름
  "htmlWhitespaceSensitivity": "css", // HTML 공백 감도 설정
  "jsxBracketSameLine": false, // JSX의 마지막 `>`를 다음 줄로 내릴지 여부
  "jsxSingleQuote": false, // JSX에 single quote 사용 여부
  "printWidth": 80, // 한 줄에 출력되는 코드의 최대 길이
  "proseWrap": "preserve", // markdown 텍스트의 줄바꿈 방식 (v1.8.2)
  "quoteProps": "as-needed", // 객체 속성에 쿼테이션 적용 방식
  "semi": true, // 세미콜론 사용 여부
  "singleQuote": true, // single 쿼테이션 사용 여부
  "tabWidth": 2, // 탭 간격
  "trailingComma": "all", // 여러 줄을 사용할 때, 후행 콤마 사용 방식
  "useTabs": false, // 탭 사용 여부
  "vueIndentScriptAndStyle": true, // Vue 파일의 script와 style 태그의 들여쓰기 여부 (v1.19.0)
  "parser": "", // 사용할 parser를 지정, 자동으로 지정됨
  "filepath": "", // parser를 유추할 수 있는 파일을 지정
  "rangeStart": 0, // 포맷팅을 부분 적용할 파일의 시작 라인 지정
  "rangeEnd": Infinity, // 포맷팅 부분 적용할 파일의 끝 라인 지정,
  "requirePragma": false, // 파일 상단에 미리 정의된 주석을 작성하고 Pragma로 포맷팅 사용 여부 지정
  "insertPragma": false, // 미리 정의된 @format marker의 사용 여부 (v1.8.0)
  "overrides": [
    {
      "files": "*.json",
      "options": {
        "printWidth": 200
      }
    }
  ] // 특정 파일별로 옵션을 다르게 지정함, ESLint 방식 사용
}
```

더 많은 옵션들은 아래의 Prettier 사이트의 playground 탭을 통해 직접 적용해보고 어떤 식으로 포맷팅되는지 확인할 수 있다.

프로젝트 초기 팀원들과 코드 포맷팅 룰을 정할 때 시각적으로 확인할 수 있다는 점에서 유용하게 사용될 것 같다.

[Prettier Playground](https://prettier.io/playground/)

## .prettierrc 파일을 생성하고 옵션 적용하기

코드 포맷팅 룰을 정했다면, root 폴더에 `.prettierrc` 파일을 생성하고 적용할 옵션들을 설정한다.

```typescript
{
  "singleQuote": true,
  "parser": "typescript",
  "semi": true,
  "useTabs": false,
  "tabWidth": 2,
  "printWidth": 120,
  "arrowParens": "always"
}
```

- `singleQuote`: quote를 single로 사용할지, double로 사용할지 결정
- `semi`: 명령문 마지막에 세미콜론 사용 여부
- `quoteProps`: 객체 안에 key의 따옴표를 필요한 경우에만 사용
- `useTabs`: 공백 대신 탭으로 줄을 들여 쓸지 여부
- `printWidth`: 라인의 최대 길이 설정 (120)
- `tabWidth`: 들여쓰기의 칸 수 (2칸)
- `arrowParens`: 화살표 함수에서 매개변수를 항상 괄호로 감싸도록 설정

어떤 룰을 적용해야할지 아직 미숙했기에, 가장 많이 사용하는 옵션들을 구글링해서 적용해보았다.

## 명령 단축어 지정하기

작성한 .prettierrc의 옵션들을 자동으로 적용할 수 있도록 명령어를 단축어로 지정할 수 있다.

```typescript
"scripts": {
	...
  "format": "prettier --write --cache src/**/*"
}
```

`format`이라는 단축어를 통해 `prettier --write --cache src/**/*` 명령어를 수행하도록 작성했다.

즉, 커맨드창에 `npm run format`만 입력하면 src 폴더에 있는 모든 파일들을 포맷팅할 수 있다.

- `--write`: Prettier가 파일을 읽고 포맷한 후, 포맷된 결과를 해당 파일에 덮어쓴다.
- `--cache`: Prettier가 이전에 포맷한 파일의 정보를 캐시에 저장하여, 이후 실행 시 변경되지 않은 파일은 다시 포맷하지 않도록 한다.

cache 파일은 `node_modules` 내부에 저장되므로 따로 `.gitignore`로 지정하지 않아도 된다.

<br />
<br />
<br />
# ESLint

!https://prod-files-secure.s3.us-west-2.amazonaws.com/13897cab-0dd6-431f-b847-04477372a586/271249c3-7046-4e4d-b024-442f1fadecfc/eslint.png

- **ES**: ECMA Script로서 JavaScript를 표준화하기 위해 만들어진 규격
- **Lint**: 소스 코드를 분석하여 오류, 버그, 코딩 스타일 등을 에러로 표시

즉, ESLint는 소스코드를 분석하여 **문법적 오류, 버그, 코드 스타일을 검사하고 에러를 표시**하며, **코드 포맷팅**도 가능하게 해주는 플러그인이다.

## ESLint 설치하기

`npm init @eslint/config` 명령어를 입력하면 아래 절차대로 ESLint를 설치할 수 있다.

그러나 **CRA**로 프로젝트를 구성했다면 기본적으로 ESLint가 설치되어 있으므로 아래의 절차대로 설치할 필요는 없다.

![스크린샷 2024-06-25 오후 9.10.11.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F8069ee46-aa95-4985-a2d7-fd46c0fe4092%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.10.11.png?id=c53e80e0-664f-435a-a3a2-5ca08304a8d7&table=block)

**1. How would you like to use ESLint?**

**❯ To check syntax, find problems, and enforce code style**

코드 구문 검사, 문제 식별, 코드 스타일 강제 적용 에서 원하는 만큼 선택한다.

![스크린샷 2024-06-25 오후 9.11.00.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F92621560-e752-4cde-bc03-bab275a2fd36%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.11.00.png?id=71c5917b-fe65-4d3d-81f7-b3e071da1934&table=block)

**2. What type of modules does your project use?**

**❯ JavaScript modules (import/export)**

import/export 를 사용하면 JavaScript modules, require/exports 를 사용하면 CommonJS 를 선택한다.

![스크린샷 2024-06-25 오후 9.11.14.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F611550ed-81a1-49ad-a013-5fa75a676fd5%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.11.14.png?id=4d91578d-8362-4fb1-9344-9f78ec5e2abd&table=block)

**3. Which framework does your project use?**

**❯ React**

본인이 해당 프로젝트에서 사용하는 프레임워크를 선택한다.

![스크린샷 2024-06-25 오후 9.14.05.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F1f971842-5fbd-4ce4-a917-eefccd5cffe4%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.14.05.png?id=654194ae-b021-4bdf-8c9a-cfed6ba7b2be&table=block)

**4. The React plugin doesn't officially support ESLint v9 yet. What would you like to do?**

**❯ Install ESLint v8.x**

리액트 플러그인은 아직 공식적으로 ESLint v9을 지원하지 않는다하여 8.x 버전을 설치하였다.

![스크린샷 2024-06-25 오후 9.14.58.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F11a79582-a824-40a8-bea5-eff6f4c1615e%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.14.58.png?id=0ba5de08-4b7d-4107-a29c-015cb7c4821b&table=block)

**5. Does your project use TypeScript?**

**❯ Yes**

자바스크립트를 사용하면 No, 타입스크립트를 사용하면 Yes 를 선택한다.

![스크린샷 2024-06-25 오후 9.15.15.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Ff815790b-48aa-4813-925f-2ef2e508695f%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.15.15.png?id=d2cd4ce8-7825-44fc-ac4d-dfb5ad5316f7&table=block)

**6. Where does your code run?**

**❯ Browser**

중복 선택 가능하며, 코드가 웹 브라우저 환경에서 실행되면 Browser, Node.js 환경에서 실행되면 Node 를 선택한다.

![스크린샷 2024-06-25 오후 9.16.26.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F98e2eca0-2680-4785-b771-a1658f99d0a8%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.16.26.png?id=7f606696-6438-4f38-80a3-5383ca5d2ff8&table=block)

**7. The config that you’ve selected requires the following dependencies**

**❯ Yes**

![스크린샷 2024-06-25 오후 9.16.38.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fdfb71288-3765-4012-9de0-827ecff60ccc%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-25_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.16.38.png?id=b2e31a7f-80f8-4fde-be3f-8dd788ef8faa&table=block)

**8. Which package manager do you want to use?**

**❯ npm**

본인이 선호하는 패키지 매니저를 선택한다.

설치가 완료되면 `eslint.config.mjs` 파일이 root 폴더에 생성된다. 익숙한 JSON 형식으로 `.eslintrc` 파일을 생성하여 다음과 같이 설정한다.

```typescript
// package.json

"devDependencies": {
  "@eslint/js": "^9.5.0",
  "eslint": "^8.57.0",
  "eslint-plugin-react": "^7.34.3",
  "globals": "^15.6.0",
  "typescript-eslint": "^7.14.1"
}
```

```typescript
// .eslintrc

{
  "env": {
    "browser": true,
    "es2021": true
  },
  "extends": ["react-app", "eslint:recommended"],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": true
    },
    "ecmaVersion": "latest",
    "sourceType": "module"
  },
  "rules": {
    "react/jsx-filename-extension": ["error", { "extensions": [".tsx"] }]
  },
  "settings": {
    "import/resolver": {
      "node": {
        "extensions": [".js", ".jsx", ".ts", ".tsx"]
      }
    }
  }
}
```

## eslint-config-airbnb

![https://prod-files-secure.s3.us-west-2.amazonaws.com/13897cab-0dd6-431f-b847-04477372a586/82b9653f-278b-4421-a020-099b0adf547b/image_(1).png](<https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F82b9653f-278b-4421-a020-099b0adf547b%2Fimage_(1).png?id=f0686f93-d9b6-4e67-9ed6-9c7d23c50109&table=block>)

현재 가장 많이 사용되는 스타일 가이드는 Airbnb에서 정의한 자바스크립트 스타일 가이드다.

`npm i -D eslint-config-airbnb` 명령어를 통해 설치하고, `.eslintrc` 파일에 `extends`에 **“airbnb”**를 추가해준다.

```typescript
{
  "env": {
    "browser": true,
    "es2021": true
  },
  "extends": ["react-app", "eslint:recommended", "airbnb"],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": true
    },
    "ecmaVersion": "latest",
    "sourceType": "module"
  },
  "rules": {
    "react/jsx-filename-extension": ["error", { "extensions": [".tsx"] }]
  },
  "settings": {
    "import/resolver": {
      "node": {
        "extensions": [".js", ".jsx", ".ts", ".tsx"]
      }
    }
  }
}
```

## eslint-config-prettier

ESLint와 Prettier의 기능이 겹치는 부분이 있어 **충돌**이 발생할 수 있다.

`eslint-config-prettier`를 설치하여 겹치는 기능을 비활성화해준다.

`npm i -D eslint-config-prettier` 명령어로 설치하고 `.eslintrc` 파일에 `extends`에 **“prettier”**를 추가한다.

```typescript
{
  "env": {
    "browser": true,
    "es2021": true
  },
  "extends": ["react-app", "eslint:recommended", "airbnb", "prettier"],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": true
    },
    "ecmaVersion": "latest",
    "sourceType": "module"
  },
  "rules": {
    "react/jsx-filename-extension": ["error", { "extensions": [".tsx"] }]
  },
  "settings": {
    "import/resolver": {
      "node": {
        "extensions": [".js", ".jsx", ".ts", ".tsx"]
      }
    }
  }
}
```

## ESLint 룰 적용해보기

`.eslintrc` 파일에 `rules` 부분에 원하는 에러 룰을 추가한다.

```typescript
{
  "env": {
    "browser": true,
    "es2021": true},
  "extends": ["react-app", "eslint:recommended", "airbnb", "prettier"],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": true},
    "ecmaVersion": "latest",
    "sourceType": "module"
  },
  "rules": {
    "react/jsx-filename-extension": ["error", { "extensions": [".tsx"] }],
    "no-var": "error",
    **"no-multiple-empty-lines": "error",
    "no-console": ["error", { "allow": ["warn", "error", "info"] }],
    "eqeqeq": "error",
    "dot-notation": "error",
    "no-unused-vars": "error"**
  },
  "settings": {
    "import/resolver": {
      "node": {
        "extensions": [".js", ".jsx", ".ts", ".tsx"]
      }
    }
  }
}
```

- `"no-var": "error"`: `var` 키워드로 변수를 선언하는 것을 금지하고 `let` 또는 `const`를 사용
- `"no-multiple-empty-lines": "error"`: 연속으로 여러 개의 빈 줄을 사용하는 것을 금지
- `"no-console": ["error", { "allow": ["warn", "error", "info"] }]`: `console` 객체의 사용을 제한
- `"eqeqeq": "error"`: `==`와 `!=` 연산자를 사용하는 것을 금지하고 `===`와 `!==`를 사용
- `"dot-notation": "error"`: 객체 속성 접근 시 점 표기법(`.`)을 사용
- `"no-unused-vars": "error"`: 선언한 변수를 사용하지 않는 것을 금지

더 많은 룰은 [ESLint 사이트](https://eslint.org/docs/latest/rules/)에서 확인할 수 있다.

## 명령 단축어 지정하기

설정한 ESLint를 자동으로 적용할 수 있도록 명령어를 단축어로 지정한다.

```typescript
"scripts": {
  "lint": "eslint --cache src/**/*"
}
```

`lint`라는 단축어를 통해 `eslint --cache src/**/*` 명령어를 수행하도록 작성했다.

커맨드창에 `npm run lint`만 입력하면 src 폴더에 있는 모든 파일들을 린팅할 수 있다.

lint도 prettier와 마찬가지로 `--cache` 옵션을 주어 캐싱되도록 하여 변경된 부분만 적용해 속도를 높일 수 있다.

ESLint는 root 폴더에 `.eslintcache` 파일을 생성하므로 `.gitignore` 해당 경로를 추가하여 GitHub에 push되지 않도록 한다.

## ESLint 절대 경로 이슈 발생

![절대경로 이슈](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F2bcd1db9-3b4d-4dbf-acf3-a5ebf3f6ba0d%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-26_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_5.44.42.png?id=df3e0305-9786-4294-a467-47459a1716bc&table=block)

ESLint가 `@components/Button.tsx` 모듈의 경로를 찾지 못할 때는 **alias resolver**가 제대로 설정되지 않아 생긴 문제였다.

이를 해결하려면 `eslint-plugin-import`와 `eslint-import-resolver-alias` 플러그인을 설치한다.

`npm install eslint-plugin-import@latest --save-devnpm install eslint-import-resolver-alias --save-dev`

설치 후 `.eslintrc` 파일에서 **import/resolver/alias** 부분에 각 절대 경로에 해당하는 alias를 지정해준다.

```typescript
{
  "env": {
    "browser": true,
    "es2021": true},
  "extends": ["react-app", "eslint:recommended", "plugin:import/typescript", "airbnb", "prettier"],
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaFeatures": {
      "jsx": true},
    "ecmaVersion": "latest",
    "sourceType": "module"
  },
  "rules": {
    "no-var": "error",
    "no-multiple-empty-lines": "error",
    "no-console": ["error", { "allow": ["warn", "error", "info"] }],
    "eqeqeq": "error",
    "dot-notation": "error",
    "no-unused-vars": "error",
    "react/jsx-filename-extension": ["error", { "extensions": [".tsx"] }],
    "import/extensions": ["error", "ignorePackages"]
  },
  "settings": {
    **"import/resolver": {
      "alias": {
        "map": [
          ["@components", "./src/components"]
        ],
        "extensions": [".js", ".jsx", ".ts", ".tsx"]
      }
    }**
  }
}
```

절대 경로 문제로 발생한 ESLint 에러가 해결된다.

## 위처럼 했지만 다시 또 이슈 발생…

![이슈발생](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fd22afd11-4a57-4e86-b9f4-745492d03ab8%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-26_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.57.16.png?id=032d9ffd-9663-4e37-9c95-b02bb6b44379&table=block)

이유를 잘 모르겠지만, 구글링한 결과 `rules`에 `"import/no-unresolved": "off"`를 추가하면 해결된다고 하여 작성했다.

파일 확장자를 무시하도록 ESLint 설정을 업데이트해보았다.

`.eslintrc` 파일에서 `import/extensions` 규칙을 수정하여 `.tsx` 확장자의 기재를 무시할 수 있도록 수정하여 해결하였다.

```typescript
"import/extensions": ["error", "ignorePackages", {
  "js": "never",
  "jsx": "never",
  "ts": "never",
  "tsx": "never"
}]
```
<br />

![스크린샷 2024-06-27 오후 3.23.14.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Ffdab6972-ecb1-4a71-b82f-6bf7c2954623%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-27_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_3.23.14.png?id=366f1244-be63-41c1-b8b7-28be1a45b31d&table=block)

<br />
<br />
<br />
# 마치면서

이번 글에서는 Prettier와 ESLint를 활용해 CRA + TypeScript 프로젝트에서 코드 스타일을 통일하고, 가독성을 높이는 방법을 다뤘다.

절대 경로 설정부터 Prettier와 ESLint의 설치 및 설정, 그리고 각종 이슈 해결 방법까지 자세히 살펴보았다.

초기 프로젝트 세팅을 통해 코드 품질을 높이고, 팀 내의 코드 스타일을 통일할 수 있기를 기대할 수 있을 것 같다.
