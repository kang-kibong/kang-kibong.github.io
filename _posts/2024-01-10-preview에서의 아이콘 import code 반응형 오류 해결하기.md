---
layout: post
title: "preview에서의 아이콘 import code 반응형 오류 해결하기"
category: react-icons
---

<br />
<br />
<br />

# 1.주제

화면 사이즈가 1025px이하로 줄어들면 import code에 있는 문자들이 바깥으로 넘어가는 [문제](https://github.com/react-icons/react-icons/issues/849)가 있었다.

<br />
<br />
<br />

# 2. 해결 과정

## preview를 살펴보다

웹페이지 상에서의 오류이기 때문에 `packages`폴더안에 `preview-astro`폴더를 확인해보았다. 작업에 들어가기에 앞서 `packages`에 있는 `react-icons`들을 fetch하고 build하였다. 이후 로컬 서버를 띄우고 페이지에 내에서의 오류를 확인해보기로 하였다.

<br />

![스크린샷 2023-11-22 오후 4.55.32.png](https://images.spr.so/cdn-cgi/imagedelivery/j42No7y-dcokJuNgXeA0ig/54e8b0f6-9bd7-499f-8092-42edbca08b0e/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-11-22_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.55.32/w=750,quality=90,fit=scale-down)

![스크린샷 2023-11-22 오후 4.55.14.png](https://images.spr.so/cdn-cgi/imagedelivery/j42No7y-dcokJuNgXeA0ig/f4d5ebec-9558-4cb2-8eb7-198ea177ef7d/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-11-22_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.55.14/w=640,quality=90,fit=scale-down)

![스크린샷 2023-11-22 오후 4.56.37.png](https://images.spr.so/cdn-cgi/imagedelivery/j42No7y-dcokJuNgXeA0ig/54e72659-df50-4da9-bdb8-dd86da546388/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-11-22_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.56.37/w=640,quality=90,fit=scale-down)

<br />

사용자가 copy하고 싶은 아이콘을 클릭하면 아래와 같은 모달창이 렌더링 된다. 확인해본 결과 화면 사이즈를 1025px이내로 줄일 경우 내부 code가 box의 영역을 침범하는 문제였다. 때문에 modal을 관리하는 component와 style코드를 확인해볼 필요가 있었다.

![스크린샷 2023-11-22 오후 4.57.38.png](https://images.spr.so/cdn-cgi/imagedelivery/j42No7y-dcokJuNgXeA0ig/605d3819-33ee-44ca-a66f-6b580467ba81/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-11-22_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.57.38/w=384,quality=90,fit=scale-down)

## 문제의 component와 style을 찾아서

`components`폴더에 `icondetailmodal.tsx`에서 관리한다는 것을 알게 되었고 문제가 있는 요소는 `<pre>`태그 내부에 있는 `<pre>`태그의 스타일 문제일 것으로 판단하였다. 해당 component의 스타일링을 담당하는 파일은 무엇인지 확인해보았다.

<br />

![스크린샷 2023-11-22 오후 5.02.59.png](https://images.spr.so/cdn-cgi/imagedelivery/j42No7y-dcokJuNgXeA0ig/76292c08-49bf-455b-8d3d-4962b27e6bab/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-11-22_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_5.02.59/w=640,quality=90,fit=scale-down)

![스크린샷 2023-11-22 오후 5.03.17.png](https://images.spr.so/cdn-cgi/imagedelivery/j42No7y-dcokJuNgXeA0ig/a9c9c4df-888f-427e-af6e-59ca74954746/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-11-22_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_5.03.17/w=750,quality=90,fit=scale-down)

<br />

`styles`폴더안에 components들의 스타일들을 담당하는 파일로 확인되었다. 수정해야될 부분의 클래스가 지정되어있는지를 확인하기위해 개발자 모드로 해당 영역의 클래스를 파악해보았다. 모달의 전체 영역에 해당하는 클래스를 확인해본 결과 `.icon-detail-modal-content` 클래스에서 스타일링을 건든다는 것을 확인하게 되었다. 해당 클래스 내부의 `<pre>` 태그를 수정하면 될 것 같다는 생각이 들었다.

<br />

![스크린샷 2023-11-22 오후 5.04.50.png](https://images.spr.so/cdn-cgi/imagedelivery/j42No7y-dcokJuNgXeA0ig/f6a173b6-ff33-493e-84c6-ff1931d5d523/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-11-22_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_5.04.50/w=640,quality=90,fit=scale-down)

![스크린샷 2023-11-22 오후 5.07.28.png](https://images.spr.so/cdn-cgi/imagedelivery/j42No7y-dcokJuNgXeA0ig/7721d651-6569-47b4-9167-60b894dbdaff/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-11-22_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_5.07.28/w=750,quality=90,fit=scale-down)

## 문제의 원인은?

`overflow`를 지정하지 않아서 생긴오류였다. pre태그 내부에 있는 요소들이 외부의 영역으로 침범하지 않도록 설정하기위해 `overflow`를 `auto`로 설정하였다.

```css
.icon-detail-modal-content {
  .icon {
    text-align: center;
    min-height: 250px;
    padding: 25px;
  }
  pre {
    background-color: #24292e;
    color: #e1e4e8;
    padding: 10px;
    border-radius: 5px;
  }
  ul.copy {
    list-style: none;
    padding: 0;
    li {
      margin-bottom: 0.5rem;
      button {
        border: none;
        background: var(--color-gray-2);
        padding: 5px 7px;
        border-radius: 5px;
        cursor: pointer;
        .text {
          margin-left: 5px;
        }
      }
    }
  }
```

```css
.icon-detail-modal-content {
  .icon {
    text-align: center;
    min-height: 250px;
    padding: 25px;
  }
  pre {
		overflow: auto;
    background-color: #24292e;
    color: #e1e4e8;
    padding: 10px;
    border-radius: 5px;
  }
  ul.copy {
    list-style: none;
    padding: 0;
    li {
      margin-bottom: 0.5rem;
      button {
        border: none;
        background: var(--color-gray-2);
        padding: 5px 7px;
        border-radius: 5px;
        cursor: pointer;
        .text {
          margin-left: 5px;
        }
      }
    }
  }
```

<br />

수정을 마치고 [PR](https://github.com/react-icons/react-icons/pull/858)을 날렸다.

<br />
<br />
<br />

# 2. 소감

이슈를 통해서 문제를 먼저 파악하고 해결하기 위해 코드를 직접 분석하는 과정이 생각보다 재미있게 느껴졌다.

해당 코드는 무슨 역할을 하는지, 전체적으로 어떻게 관리되어지는지에 대한 흐름을 파악하는 과정이 매우 새롭게 느껴지기도 했다.

그리고 처음 시도해본 오프소스 기여인데 머지 당해버렸다!

막상 해보니 생각보다 별거 아니라는거에 감회가 새로웠고, 기여에 대한 막연한 어려움을 극복할 수 있는 기회가 된 것 같다.

내 수준에 해결할 수 있는 또 다른 이슈들은 무엇이 있는지 한번 찾아보고 해결해보는 것에 도전해봐야겠다.

![merged](https://images.spr.so/cdn-cgi/imagedelivery/j42No7y-dcokJuNgXeA0ig/a01c2a0c-8cd3-4b9e-9b78-bb5fa0e11fd7/img/w=1080,quality=90,fit=scale-down)

![merged](https://images.spr.so/cdn-cgi/imagedelivery/j42No7y-dcokJuNgXeA0ig/873316cb-3725-4659-9e45-72a6794967f5/img/w=1080,quality=90,fit=scale-down)
