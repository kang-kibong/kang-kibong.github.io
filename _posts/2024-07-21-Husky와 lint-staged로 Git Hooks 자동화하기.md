---
layout: post
title: "Husky와 lint-staged로 Git Hooks 자동화하기"
category: frontend
---

<br />
<br />
<br />

# Husky란?

![v67mrvpgrqg19k3ifgll.webp](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F2a7d41e3-1dfd-4bbb-8d8e-bc17636a22f5%2Fv67mrvpgrqg19k3ifgll.webp?id=68d2ae33-ddc9-4e5d-80d3-52fb98ae96c7&table=block)

Husky는 Git의 특정 이벤트(commit, add, push) 전후로 특정 hook을 동작할 수 있도록 하는 도구이다.

개발 과정에서 패키지를 설치하고 룰을 설정해도 작업자가 이를 실행하지 않으면 효과가 없기 때문에 특정 상황에서 자동화를 통해 실행의 강제성을 부여하면 좋다.

가령, commit된 코드는 무조건 formatting되어야 하고 push된 코드는 무조건 eslint가 pass되어야 한다는 규칙을 자동화로 구현할 수 있다.

하지만 이러한 자동화를 위해 Git Hook을 직접 설정하기에는 매우 까다롭다.

이를 간편하게 해결할 수 있는 패키지가 Husky이다.

## Husky 실행하기

다음 명령어를 입력하면 Husky 라이브러리 설치와 동시에 실행이 완료된다.

```tsx
npx husky init
```

해당 명령어를 입력하면, root폴더에 .husky라는 폴더와 함께 pre-commit이라는 파일이 생성된 것을 확인할 수 있다.

![스크린샷 2024-07-08 오후 6.33.18.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F73af546a-e8f6-4b1d-bf77-01b48e1643cf%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-08_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.33.18.png?id=3dca641f-699b-4d2b-ac0c-af7e3b9944d5&table=block)

이후 사용자들이 프로젝트를 clone 받고 npm install을 하면 자동으로 husky가 실행될 수 있도록 package.json에 prepare를 통해 설정한다.

```tsx
{
  "scripts": {
    "format": "prettier --write --cache src/**/*.{ts,tsx}",
    "lint": "eslint --cache src/**/*.{ts,tsx}",
    "prepare": "husky install"
  },
  "devDependencies": {
    "eslint": "^8.47.0",
    "eslint-config-prettier": "^9.0.0",
    "husky": "^8.0.3",
    "prettier": "^3.0.2"
  }
}
```

## hook 추가하기

우리는 commit 하기 이전에 ESLint와 Prettier 명령어가 수행되었으면 좋겠다는 가정하에 진행해보자.

참고로 ESLint와 Prettier를 통한 초기 프로젝트 세팅 관련한 내용은 이전에 포스팅한 내용을 통해 보다 자세하게 확인할 수 있다.

https://llbllhllk.tistory.com/132

다음과 같은 명령어를 통해 pre-commit에 명령어를 추가할 수 있다.

```tsx
echo "npm run format\nnpm run lint" > .husky/pre-commit
```

## commit한 결과

![스크린샷 2024-07-21 오후 8.57.52.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F64f1fd73-4e29-469b-b196-6a45b9c9efcf%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-21_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_8.57.52.png?id=69e4c08f-98dc-46a4-a11d-f414e9fec438&table=block)

이후 커밋을 진행하면 다음과 같이 Prettier와 ESLint가 실행을 마친 후 커밋이 진행되는 것을 확인할 수 있다.

하지만 수행하는 데 있어 꽤 긴 시간을 요한다.

이는 ESLint와 Prettier가 모든 파일을 검사하기 때문이다.

이를 더 효율적으로 처리할 수 있는 방법은 없을까?

이러한 문제는 lint-staged 라이브러리를 통해 해결할 수 있다.

<br />
<br />
<br />

# lint-staged란?

![스크린샷_2024-07-21_오후_9.23.17-removebg-preview.png](https://blog.kakaocdn.net/dn/dHwMpv/btsIGLzDdKT/kYhHrKpU06BjJKvhNUqGv0/img.png)

lint-staged는 Git에 add한 파일들을 기준으로 검사할 수 있도록 하는 패키지이다.

Husky만 사용했을 경우 모든 파일을 검사하기 때문에 비효율적일 수 있지만, lint-staged를 사용하면 staged된 파일들만 검사하여 효율적으로 특정 Git Hook을 수행할 수 있다.

## lint-staged 설치하기

다음 명령어를 통해 lint-staged를 설치할 수 있다.

이후 lint-staged를 수행하기 위한 몇 가지 설정을 진행해야한다.

```bash
npm install lint-staged --save-dev
```

## lint-staged 설정 및 초기화하기

먼저 package.json 파일에 `lint-staged`를 정의하여 tsx, ts, jsx, js 파일들을 Prettier와 ESLint로 검사할 수 있도록 설정한다.

```typescript
{
  "scripts": {
    "lint-staged": "lint-staged"
  },
  "lint-staged": {
    "**/*.{tsx,ts,jsx,js}": ["eslint --fix --cache", "prettier --write --cache"]
  }
}
```

이후 lint-staged에 정의했던 명령어들이 수행할 수 있도록 git과 lint-staged 패키지를 초기화해야한다.

```bash
npx mrm lint-staged
```

해당 명령어를 실행하면 프로젝트의 루트 폴더에 .husky 폴더에 있는 pre-commit에, `npx lint-staged` 명령어가 담긴 pre-commit 후크가 생성된다.

![이미지](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Facbef8b1-ef8e-4f13-b761-b4023f180943%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-20_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_7.58.15.png?id=01e4ec5b-432b-4128-9480-3ce7b2d6ba34&table=block)

## lint-staged 결과

![스크린샷 2024-07-21 오후 8.59.53.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fc602a3ff-9126-46d5-b18b-1128581b8233%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-21_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_8.59.53.png?id=b7df6378-3d5c-42a5-b490-9afafafa7b9f&table=block)

![스크린샷 2024-07-21 오후 9.00.03.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F4db85a8e-200c-485f-ab6a-29748b9b11a4%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-07-21_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.00.03.png?id=fd5fb3a4-261a-4448-93fb-d45391de78c6&table=block)

Husky를 단독으로 사용했을 때보다 staged된 파일만 검사하기 때문에 더욱 빠르게 처리하는 것을 확인할 수 있다.

<br />
<br />
<br />

# ⚠️ Husky 관련 에러

`힌트: '.husky/pre-commit' 후크가 실행 가능하도록 설정되지 않아서, 무시됩니다.` 라는 에러가 발생하는 것은 .husky 폴더의 권한을 부여하지 않았기 때문이다.

때문에 두 가지 설정을 모두 해주어야 진행해야한다.

- 깃 파일 모드 추적 끄기
  - `git config core.filemode false`
- 해당 파일에 실행 권한 추가하기
  - `chmod +x .husky/pre-commit`

<br />
<br />
<br />

# 마치면서

이번 글에서는 Husky와 lint-staged를 활용해 Git Hooks를 자동화하는 방법에 대해 살펴보았다.

Husky를 통해 Git의 특정 이벤트에 Hook을 추가하여 자동으로 특정 작업을 실행할 수 있었고, lint-staged를 사용하여 Git에 add된 파일들만을 검사함으로써 효율적으로 코드를 관리하는 방법을 다루어보았다.

해당 라이브러리들을 도입함으로써 일관된 코드베이스를 유지하고, 개발 과정에서 발생할 수 있는 오류를 사전에 방지할 수 있을 것이다.

이러한 자동화 도구들을 활용하여 프로젝트의 품질과 효율성을 높일 수 있길 기대한다.
