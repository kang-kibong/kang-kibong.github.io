---
layout: post
title: "preview에서의 multi word 검색 기능 개선하기"
category: react-icons
---

<br />
<br />
<br />

# 1. 주제

이전 [이슈](https://github.com/react-icons/react-icons/issues/796)에서 사용자가 입력한 검색에 공백이 있을 경우 아이콘 검색이 안나오는 경우가 발생하는 오류가 있었다.

때문에 사용자의 입력을 처리하여 더 낫은 검색 방식을 제공하자는 issue가 나왔지만 아쉽게도 이미 merge가 되서 기여를 하지 못했었다.

하지만 preview가 astro 프레임워크로 변환되면서 해당 문제가 동일하게 발생한다는 것을 알게 되었다.

이를 기회로 해당 문제에 대해서 다시 [issue를 등록](https://github.com/react-icons/react-icons/issues/859)하고 PR을 올리기로 하였다.

<br />
<br />
<br />

# 2. 해결 과정

## query를 다루는 프로세스

![스크린샷 2023-11-22 오후 9.20.39.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F35a5ca70-aae3-454d-83e2-4c9310b47d46%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-11-22_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.20.39.png?id=6f4e026d-b1be-4dd1-a465-f4ecfd5f54e3&table=block)

![스크린샷 2023-11-22 오후 9.19.33.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Ffec8fd94-5430-4bbd-86ed-3565139cfd82%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-11-22_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.19.33.png?id=0ef801aa-f1ff-429d-a319-6c1c68eabccd&table=block)

<br />

사용자 검색에 해당하는 `searchinput` 컴포넌트를 살펴보게 되었다.

사용자의 입력값을 `inputQuery` state로 관리한다는 것을 알게 되었다.

해당 `inputQuery` state를 디바운싱하는 동시에 `useSearch` custom hook을 통해 전역으로 state를 관리하도록 하는 것을 알 수 있었다.

이후 해당 `query`를 가지고 검색 결과에 해당하는 `index`컴포넌트에서 먼저 아이콘들을 순회하여 `SearchIconSet` 컴포넌트로 렌더링하고 와 일치하는 아이콘들을 반환하고 있는 것을 알 수 있었다.

## 문제 해결

![스크린샷 2023-11-22 오후 9.23.22.png](https://app.super.so/_next/image?url=https%3A%2F%2Fassets.super.so%2F103836b2-5162-4915-b9b3-3e82eeac52e9%2Fimages%2F679692b9-d1fa-474a-8cd8-dd2b6ebc8abc%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2023-11-22_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_9.23.22.png&w=1080&q=90)

<br />

프로세스를 알게된 이후 `searchInput` 컴포넌트 내부에서 디바운싱 하기전의 `query`를 공백을 기준으로 배열을 분리하고 다시 합쳐 하나의 문자열 형태로 가공한 후 `sanitizedQuery`라는 변수에 할당하였다.

이를 디바운싱하고 전역으로 관리하도록 수정하였다.

수정을 마치고 [PR](https://github.com/react-icons/react-icons/pull/860)을 날렸다.

<br />
<br />
<br />

# 3. 소감

![helpful](https://blog.kakaocdn.net/dn/b6sFoK/btsGyGU4oW4/ejynl6OgIUIAkJt9pfuFC1/img.png)

<br />

내가 등록한 issue와 기존에 있었던 비슷한 issue를 close하고 직접 개선하신 것 같다.

긍정적으로 생각해보면 해당 issue와 관련해서 내가 PR을 날리지 않았다면 개선 조차 안되었을 것이다.

비록 머지 당하지는 않았지만, 또 하나의 문제를 개선했다는 점에서 만족하기로 하였다.
