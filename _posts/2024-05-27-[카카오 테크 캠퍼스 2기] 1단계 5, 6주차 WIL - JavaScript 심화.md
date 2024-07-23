---
layout: post
title: "[카카오 테크 캠퍼스 2기] 1단계 5·6주차 WIL - JavaScript 심화"
category: kakao1
---

<br />
<br />
<br />

# 1. Modules

## import와 export 패턴

### export

```jsx
export default 123;

export const str = "abc";
export const arr = [];
export function hello() {}
```

- 여러개의 데이터를 내보낼 때 `export` 키워드를 사용한다.
- 하나의 데이터를 내보낼 때 `export default` 키워드를 사용한다.

### import

```javascript
import { str as string, arr, hello as helloFunction } from "./module.js";
console.log(string, arr, helloFunction);

import * as object from "./module.js";
console.log(object.str, object.arr, object.hello());
```

- `as`라는 키워드를 통해 기존 데이터의 이름을 변경해서 사용할 수 있다.
- `*` 애스터리스크를 사용하면 해당 파일에 있는 export한 모든 데이터를 객체 형태로 사용할 수 있다.

## 가져온 모듈 바로 내보내기

```jsx
// a.js
export const a = () => 123;

// b.js
export const a = () => 456;

// utils.js
export { a } from './a.js'
export { b } from './a.js'

// main.js
import { a, b } from './utils.js
```

- `a.js`, `b.js` 파일에서 내보낸 `a()`함수와 `b()`함수를 `utils.js` 파일에서 `export` 키워드를 통해 바로 두 함수를 export한다.
- `main.js`에서 `utils.js` 파일에서 내보낸 두 함수를 `import`하면 두 함수를 한꺼번에 import할 수 있다.

<br />
<br />
<br />

# 2. 비동기

비동기 함수에서 callback 함수를 실행할 때 호출의 순서를 보장하기 위해서는 다양한 패턴들이 존재한다.

## 콜백 패턴

```jsx
const getMovies = (movieName, callback) => {
  fetch(`https://www.omdbapi.com/?apikey=7035c60c&s=${movieName}`)
    .then((res) => res.json())
    .then((res) => {
      console.log(res);
      callback();
    });
};

// callback hell!
getMovies("frozen", () => {
  console.log("겨울 왕국!");
  getMovies("avengers", () => {
    console.log("어벤져스!");
    getMovies("avatar", () => {
      console.log("아바타!");
    });
  });
});
```

- **콜백지옥**이 발생한다. Promise 클래스를 사용하면 개선가능하다.

## Promise 패턴

```jsx
const getMovies = (movieName) => {
  return new Promise((resolve) => {
    fetch(`https://www.omdbapi.com/?apikey=7035c60c&s=${movieName}`)
      .then((res) => res.json())
      .then((res) => {
        console.log(res);
        resolve();
      });
  });
};

getMovies("frozen")
  .then(() => {
    console.log("겨울왕국!");
    return getMovies("avengers");
  })
  .then(() => {
    console.log("어벤져스!");
    return getMovies("avatar");
  })
  .then(() => console.log("아바타!"));
```

- Promise 객체의 `resolve`를 통해 순서 보장을 위한 callback의 역할을 대신하고 있다.
- Promise를 return하면 Promise객체의 `then()` 메서드를 사용할 수 있으며, `then()` 메서드에 콜백함수를 넘기면 resolve를 호출하여 실행되는 형태이다.
- `then()` 메서드는 chaining 형태로 이어 붙여 사용할 수 있다. 과도하면 콜백지옥과 비슷한 **Promise chaining**으로 인해 가독성이 떨어질 수 있다는 단점이 있다.

## async, await 패턴

```javascript
const getMovies = (movieName) => {
  return new Promise((resolve) => {
    fetch(`https://www.omdbapi.com/?apikey=7035c60c&s=${movieName}`)
      .then((res) => res.json())
      .then((res) => {
        console.log(res);
        resolve();
      });
  });
};

const wrap = async () => {
  await getMovies("frozen");
  console.log("겨울왕국!");
  await getMovies("avengers");
  console.log("어벤져스!");
  await getMovies("avatar");
  console.log("아바타!");
};

wrap();
```

- `async` 함수 내부에서만 `await` 키워드를 사용할 수 있다.
- Promise 인스턴스를 반환하는 곳에서만 `await` 키워드를 사용하도록 한다.
- Promise chaning이 발생하지 않는다는 점에서 가독성이 더 좋은 것을 확인할 수 있다.

## 에러 핸들링

### 콜백패턴

```jsx
const delayAdd = (index, cb, errorCb) => {
  setTimeout(() => {
    if (index > 10) {
      errorCb(`${index}는 10보다 클 수 없습니다.`);
      return;
    }
    console.log(index);
    cb(index + 1);
  }, 1000);
};

delayAdd(
  4,
  (res) => console.log(res),
  (err) => console.error(err)
);
```

### Promise 패턴

```jsx
const delayAdd = (index) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (index > 10) {
        reject(`${index}는 10보다 클 수 없습니다.`);
        return;
      }
      console.log(index);
      resolve(index + 1);
    }, 1000);
  });
};

delayAdd(4)
  .then((res) => console.log(res))
  .catch((err) => console.log(err))
  .finally(() => console.log("done!"));
```

- then, catch, finally 메서드를 통해서 에러 핸들링이 가능하다.

### async, await 패턴

```javascript
const wrap = async () => {
  try {
    const res = await delayAdd(12);
    console.log(res);
  } catch (err) {
    console.error(err);
  } finally () {
	  console.log('done!')
  }
};

wrap();
```

- try, catch, finally 구문을 통해서 에러 핸들링이 가능하다.

## 반복문에서 비동기 처리

반복문을 통해 순차적으로 비동기 처리를 하려면 forEach가 아닌 for을 사용해야한다.

### forEach를 통한 비동기 처리

```jsx
titles.forEach(async title => {
  const movies = await getMovies(title);
  return movies;
});

===

for (const title of titles) {
  async () => await getMovies(title);
}
```

위 forEach 코드를 아래처럼 for문 코드로 나타낼 수 있다.

아래의 코드를 살펴보면 for문이 도는 외부 context와 콜백함수의 내부 context로 이루어져 있는 것을 확인할 수 있다.

이처럼 코드가 진행되면 내부 context에서 async, await을 통한 비동기 처리를 해도, 바깥 for의 외부 context 때문에 비동기 처리가 순차적으로 진행된다는 보장은 떨어지게 된다.

때문에 for의 context를 감싸는 async context와 내부의 await context로 이루어지도록 변경해야할 필요가 있다.

### for을 통한 비동기 처리

```jsx
const wrap = async () => {
  for (const title of titles) {
    const movies = await getMovies(title);
    console.log(title, movies);
  }
};
```

기존의 async, awiat을 내부 context에서 비동기 처리를 진행했다면 wrap을 통해 async를 진행하고 내부 context는 await하여 순차적으로 비동기 처리를 진행하도록 작성할 수 있다.

이렇게 되면 순서에 대한 보장이 가능하다.

## fetch

`fetch(주소, 옵션)` 네트워크를 통해 리소스의 요청 및 응답 처리를 할 수 있다.

주소를 통해 받은 결과를 then() 메서드를 통해 확인할 수 있고 json() 메서드를 호출 하면 Promise 인스턴스를 반환하게 된다. 해당 인스턴스에 대한 결과를 then() 메서드로 확인하면 응답에 대한 결과가 json 형태의 객체로 반환되게 된다.

```typescript
const wrap = async () => {
	const res = fetch('https://omdbapi.com/?apikey=7035c60c&s=avengers', {
		method: 'GET'
		headers: 'Content-Type': 'application/json',
	});
	const json = await res.json(); // Promise 인스턴스를 반환한다.
	console.log(json);
}

wrap()
```

### option

- `method`: default는 GET method이다. 다른 method들은 옵션에 기재해주어야 한다.
- `headers` : 요청에 대한 정보에 해당한다.
  - Content-Type: 서버로 전송되는 데이터 타입 기재(application/json)
- `body`: 요청에 대한 데이터에 해당한다.
  - `JSON.stringify()`: 인수(객체)로 들어온 데이터들을 **문자열**로 타입으로 변환하는 과정이 필요하다.

<br />
<br />
<br />

# 3. Events

## 기본 동작 방지

`event.preventDefault()`

요소마다 브라우저가 가지고 있는 기본 동작을 방지할 수 있다.

```jsx
const anchorEl = document.querySelector("click", (e) => {
  e.preventDefault();
  console.log("click!");
});
```

`<a></a>` 태그가 가지고 있는 기본 동작인 페이지 이동을 이벤트 객체의 preventDefault() 메서드를 호출함으로써 페이지 이동 동작을 막을 수 있다.

## 버블링과 캡처링

### 버블링(이벤트 전파)

특정 요소에 이벤트가 발생되었을 경우, 해당 요소로부터 상위요소로 이벤트가 점차 전파되는 현상을 말한다.

```jsx
const parentEl = document.querySelector(".parent");
const childEl = document.querySelector(".child");
const anchorEl = document.querySelector("a");

window.addEventListener("click", (e) => {
  console.log("window");
});

document.body.addEventListener("click", (e) => {
  console.log("body");
});

parentEl.addEventListener("click", (e) => {
  console.log("parent");
});

childEl.addEventListener("click", (e) => {
  console.log("child");
});

anchorEl.addEventListener("click", (e) => {
  console.log("anchor");
});
```

anchor 요소를 클릭한다고 가정하면, 버블링 현상으로 인해 anchor ⇒ child ⇒ parent ⇒ body ⇒ window가 콘솔에 출력될 것이다.

![스크린샷 2024-05-18 오후 5.28.33.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fe060c442-075d-4d21-9d6e-56c53c0d4c1c%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-05-18_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_5.28.33.png?id=34aa0952-9c95-4f91-af07-3b6a25cd9894&table=block)

### 버블링 방지

`event.stopPropagation()`

특정 요소에 이벤트를 줄 경우 이벤트 객체의 `stopPropagation()` 메서드를 호출하면, 호출한 해당 요소까지만 이벤트가 전파되고 그 상위 요소들은 이벤트가 전파되는 것을 막을 수 있어 의도치 않은 동작을 막을 수 있다.

```jsx
const parentEl = document.querySelector('.parent');
const childEl = document.querySelector('.child');
const anchorEl = document.querySelector('a');

window.addEventListener('click', e => {
  console.log('window');
});

document.body.addEventListener('click', e => {
  console.log('body');
});

parentEl.addEventListener('click', e => {
  console.log('parent');
  **e.stopPropagation();**
});

childEl.addEventListener('click', e => {
  console.log('child');
});

anchorEl.addEventListener('click', e => {
  console.log('anchor');
});
```

위와 같이 parentEl 요소에 이벤트가 발생할시 `stopPropagation()` 메서드를 호출하고 anchor 요소를 클릭했을 경우 body와 window를 제외한 `anchor ⇒ child ⇒ parent`가 출력될 것이다.

![스크린샷 2024-05-18 오후 5.33.08.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F93e93880-491f-4436-8157-0756b6eb5e02%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-05-18_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_5.33.08.png?id=52364cda-adb0-4acb-a57a-15d86bd52af5&table=block)

다른 예로, parentEl 요소를 클릭하면, parentEl에 버블링 정지 처리를 했기 때문에 parentEl만 출력될 것이고, body를 클릭하면 버블링 정지 처리를 하지 않았기 때문에 `body ⇒ window`가 콘솔에 출력될 것이다.

### 캡처링

특정 요소에 이벤트가 발생되면 특정요소의 상위 요소들에 이벤트가 전파되는 버블링 현상이 발생한다. 여기서 특정 요소를 먼저 이벤트가 동작할 수 있게 설정할 수 있는데 이를 캡쳐링이라고 한다.

```jsx
const parentEl = document.querySelector(".parent");
const childEl = document.querySelector(".child");
const anchorEl = document.querySelector("a");

window.addEventListener(
  "click",
  (e) => {
    console.log("window");
  },
  { capture: true }
);

document.body.addEventListener(
  "click",
  (e) => {
    console.log("body");
  },
  { capture: true }
);

parentEl.addEventListener("click", (e) => {
  console.log("parent");
});

childEl.addEventListener("click", (e) => {
  console.log("child");
});

anchorEl.addEventListener("click", (e) => {
  console.log("anchor");
});
```

위와 같이 window와 body이벤트에 capture을 true로 부여하여 버블링 발생시 window와 body가 순차적으로 먼저 이벤트가 동작될 수 있도록 설정한다.

이후 child 요소를 클릭한다고 가정하면 window ⇒ body ⇒ parent ⇒ child 가 콘솔에 출력될 것이다.

![스크린샷 2024-05-18 오후 6.13.25.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F527b80db-dc40-4450-99b1-5d6e46bf3e58%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-05-18_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.13.25.png?id=8395abcc-e491-436a-a040-b2e1a8ffef43&table=block)

### 버블링 방지 & 캡쳐링

버블링 정지와 캡쳐링을 둘다 설정하면 어떤 결과가 나오는지 살펴보자.

```jsx
const parentEl = document.querySelector(".parent");
const childEl = document.querySelector(".child");
const anchorEl = document.querySelector("a");

window.addEventListener(
  "click",
  (e) => {
    console.log("window");
  },
  { capture: true }
);

document.body.addEventListener(
  "click",
  (e) => {
    console.log("body");
  },
  { capture: true }
);

parentEl.addEventListener("click", (e) => {
  console.log("parent");
});

childEl.addEventListener("click", (e) => {
  console.log("child");
  e.stopPropagation();
});

anchorEl.addEventListener("click", (e) => {
  console.log("anchor");
});
```

window와 body에 캡처링을 하고 childEl에 stopPropagation을 주어 버블링을 방지하였다.

childEl 요소에 이벤트를 주었다고 가정할 때 먼저 캡처링부터 살펴보자.

window와 body를 캡처링하도록 설정하였기 때문에 이를 고려하면 다음과 같은 결과로 예상할 수 있다.

`window ⇒ body ⇒ child ⇒ parent`

이제는 버블링 방지를 살펴보자.

현재 childEl에 stopPropagation메서드를 호출하고 있고 childEl으로부터의 상위 요소들은 버블링이 방지 된다는 것을 유추할 수 있다.

이를 고려하면 다음과 같은 결과를 예상할 수 있다.

`child`

이벤트 처리는 버블링 방지보다 캡처링이 우선이기 때문에 window, body는 캡처링이 진행되어야 하고 이후 버블링 방지는 parentEl 요소만 해당되기 때문에 다음과 같은 결과가 나타난다.

`window ⇒ body ⇒ child`

### 요소 캡처링 시 이벤트 제거

특정 요소에 이벤트 처리시 캡처링을 진행하였다면 이벤트 제거시에도 같이 캡처링을 설정해야한다.

```jsx
const parentEl = document.querySelector("parent");

const handler = () => {
  console.log("parent");
};

parentEl.addEventListener("click", handler, { capture: true });
parentEl.removeEventListener("click", handler, { capture: true });
```

## 이벤트 위임(event delegation)

이벤트가 발생하면 버블링이 발생한다. 이러한 점을 이용해서 여러 요소들을 모두 살펴서 이벤트를 실행시켜 메모리를 비효율적으로 사용하는 것이 아닌 여러 요소를 갖는 부모요소에게 이벤트를 위임하는 방법을 의미한다.

```jsx
const parentEl = document.querySelector(".parent");
const childEls = document.querySelectorAll(".child");

childEls.forEach((el) => {
  el.addEventListener("click", (e) => {
    console.log(e.target.textContent);
  });
});
```

위 코드와 같이 자식 요소들을 모두 하나씩 살펴가며 이벤트를 처리하는 것은 비효율적으로 메모리를 사용하게 된다.

이러한 코드를 버블링 현상을 이용하여 부모에게만 이벤트를 위임하는 방식으로 변경할 수 있다.

```jsx
const parentEl = document.querySelector(".parent");
const childEls = document.querySelectorAll(".child");

parentEl.addEventListener("click", (e) => {
  const childEl = e.target.closest(".child");
  if (childEl) console.log(childEl.textContent);
});
```

부모 요소에게 한번만 이벤트 처리를 하고 부모 요소를 클릭했을때 내부에 `.child` 클래스를 갖는 요소를 `closest()` 메서드를 통해 찾고 childEl에 할당한다.

`closest()` 메서드는 특정 요소를 찾지 못하면 null을 반환하므로 조건문을 통해 Falsy한 값이 아닌 경우 요소의 텍스트를 출력하도록 작성할 수 있다.

이처럼 자식 요소돌의 이벤트 처리를 부모요소에 한번만 이벤트를 위임하면 메모리를 효율적으로 사용할 수 있게 된다.

<br />
<br />
<br />

# 4. Cookie & Storage

## Cookie

도메인 단위로 데이터를 저장할 수 있고, 영구적으로 저장 불가능하다.

또한 표준안 기준으로 사이트당 최대 20개 및 4KB 제한되어있다.

개발자도구에 application 탭에 있는 저장용량 카테고리에 있는 쿠키를 통해서 데이터 확인이 가능하다.

- **domain**: 유효 도메인 설정
- **path**: 유효 경로 설정
- **expires**: 만료 날짜 설정(UTC Date 기준으로 설정해야한다.)
- **max-age**: 만료 타이머(s) 설정

expires나 max-age를 설정하지 않으면 expires가 세션으로 설정된다.

해당 의미는 브라우저가 종료되면 데이터가 사라진다는 뜻이다.

때문에 브라우저가 종료되더라도 데이터를 보존하고 싶으면 expires나 max-age를 명시해주어야한다.

```jsx
document.cookie = `a=1; domain=localhost; expires${new Date(
  2024,
  0,
  16
).toUTCString()} path=/abc; max-age=3`;

console.log(document.cookie); // a=1;

const getCookie = (name) => {
  const cookie = document.cookie
    .split("; ")
    .find((cookie) => cookie.split("=")[0] === name);
  return cookie ? cookie.split("=")[1] : null;
};

console.log(getcookie("a"));
```

쿠키는 만료 날짜나 시간을 설정해서 관리할 수 있어 중요한 정보를 저장하는데는 장점이 있지만, 용량 제한이 있다보니 일반적인 데이터를 브라우저에 저장하는 용도로를 적합하지 않다.

또한 데이터가 하나의 String으로 가져오는 방식이기 때문에 데이터를 다루는데 있어 다소 복잡하다.

## Storage

도메인 단위로 저장할 수 있고 세션을 통한 영구 저장이 가능하다.

또한 사이트당 5MB 제한으로 제한되어있다.

개발자도구에 application 탭에 있는 저장용량 카테고리에 있는 로컬 스토리지 혹은 세션 스토리지를 통해 데이터 확인이 가능하다.

### Storage 종류

- localStorage: 따로 제거하지 않으면 영구적으로 데이터 저장
- sessionStorage: 브라우저 세션이 유지되는 동안에만 데이터 저장

### 데이터 관리 메서드

- `getItem()`: 데이터 조회
- `setItem()`: 데이터 추가
- `removeItem()`: 데이터 제거
- `clear()`: 스토리지 초기화

```jsx
localStorage.setIem("a", JSON.stringify("Hello World"));
localStorage.setIem("b", JSON.stringify({ x: 1, y: 2 }));
localStorage.setIem("c", JSON.stringify(123));

console.log(JSON.parse(localStorage.getItem("a"))); // 'Hello World!'
console.log(JSON.parse(localStorage.getItem("b"))); // {x: 1, y: 2}
console.log(JSON.parse(localStorage.getItem("c"))); // 123
```

JSON 문자는 작은 따옴표를 허용하지않고 큰 따옴표만 허용이 된다.

그러나 storage에서 데이터를 가져오면 작은 따옴표 형태로 나올 수 있기 때문에, JSON문자와 Javascript 문자를 일치 시키기 위해 문자 데이터도 `JSON.stringify()` 메서드를 통해서 JSON 문자 형태로 변환해주도록 해야한다.

## Cookie vs localStorage vs sessionStorage

- **Cookie**: **브라우저 종료** 혹은 **만료 시간**을 통한 데이터 제거를 원할 때 사용한다. ex) 오늘 하루 보지 않기
- **localStorage**: 쿠키보다 데이터 관리가 쉽고 **만료되지 않는 데이터**를 관리할 때 사용한다. ex) 일반적인 데이터
- **sessionStorage**: 브라우저 종료를 통한 데이터 제거를 원할 때 사용한다.
