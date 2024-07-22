---
layout: post
title: "CRA + TypeScript 프로젝트 환경에서의 절대 경로 설정하기"
category: frontend
---

<br />
<br />
<br />
# tsconfig.json 설정을 통한 절대 경로 설정하기

기존의 import 경로를 상대경로로 설정하면 코드의 길이가 불필요하게 길어지는 단점이 있습니다. 이를 해결하기 위해 절대 경로를 설정하여 불필요한 코드의 길이를 줄일 수 있습니다.

먼저, `tsconfig.json` 파일의 `compilerOptions`에 `baseUrl`을 `"."`으로 설정하고 `paths`를 통해 경로를 설정해줍니다.

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "src/*": ["src/*"]
    }
  }
}
```

이 설정은 `src`를 루트로 했을 때는 동작하지만, `src`를 제외한 다른 경로로 aliasing을 시도하면 작동하지 않는 문제를 겪을 수 있습니다. 이는 내부 webpack 설정 때문에 절대 경로를 인식하지 못하기 때문입니다.

webpack 설정을 직접 수정하려면 `npm run eject` (yarn의 경우 `yarn eject`) 명령어를 실행해야 하는데, 이는 한 번 실행하면 되돌릴 수 없어 권장하지 않습니다. 대신, craco 라이브러리를 사용하면 해결할 수 있습니다.

<br />
<br />
<br />
# craco를 통한 절대 경로 설정하기

먼저, `npm i -D @craco/craco` 명령어로 craco 라이브러리를 설치합니다.

craco 설치가 완료되면 `craco.config.js` 파일을 생성하고, `src` 경로와 `components` 경로를 다음과 같이 aliasing할 수 있도록 작성합니다.

```jsx
const path = require("path");

module.exports = {
  webpack: {
    alias: {
      "@": path.resolve(__dirname, "src/"),
      "@components": path.resolve(__dirname, "src/components"),
    },
  },
};
```

마지막으로 `package.json` 파일에서 `scripts` 부분의 `react-scripts`를 `craco`로 변경해줍니다.

```json
"scripts": {
  "start": "craco start",
  "build": "craco build",
  "test": "craco test"
}
```

<br />
<br />
<br />
# 절대 경로 자동완성 이슈

이후 `components` 폴더에 있는 `Button` 컴포넌트를 절대 경로로 입력하려고 할 때 자동완성이 되지 않고 상대경로로 자동완성되는 문제가 발생할 수 있습니다.

이 문제를 해결하려면 **jsconfig.json** 또는 **tsconfig.json**의 `compilerOptions` 내부에 `baseUrl`과 `paths`를 추가해주면 됩니다. 필자는 해당 `paths`와 `baseUrl`을 다른 파일로 분리하고 싶어 `tsconfig.paths.json` 파일을 따로 만들어서 다음과 같이 paths를 분리해주었습니다.

```json
{
  "compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@components/*": ["./src/components/*"]
    }
  }
}
```

이후 `extends`를 통해 해당 `tsconfig.paths.json` 파일을 추가해주었습니다.

```json
{
  "compilerOptions": {
    ...
  },
  "include": ["src"],
  "extends": "./tsconfig.paths.json"
}
```

`components` 폴더에 임의의 `Button` 컴포넌트를 생성하고 `App`에서 `Button` 컴포넌트를 절대경로로 import 해본 결과, 정상적으로 절대경로로 자동완성해주는 것을 확인할 수 있었습니다.

<br />

![sdf](https://images.spr.so/cdn-cgi/imagedelivery/j42No7y-dcokJuNgXeA0ig/f47d5f1d-4190-4d5c-a657-80691aef8510/Jun-25-2024_16-14-26/w=1080,quality=90,fit=scale-down)

<br />
<br />
<br />
# 마치면서

이번 글에서는 `tsconfig.json`과 `craco`를 활용하여 CRA + TypeScript 프로젝트에서 절대 경로를 설정하는 방법을 다루었습니다.

더 깔끔하고 유지보수하기 쉬운 코드를 작성하면서 절대 경로 설정을 통해 생산성도 높아지고 코드 관리도 수월해지길 기대할 수 있을 것 같습니다.
