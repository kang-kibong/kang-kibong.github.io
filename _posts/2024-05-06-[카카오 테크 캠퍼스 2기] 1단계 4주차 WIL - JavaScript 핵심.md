---
layout: post
title: "[카카오 테크 캠퍼스 2기] 1단계 4주차 WIL - JavaScript 핵심"
category: kakao1
---

<br />
<br />
<br />

# 1. 연산자

## 산술 연산자(Arithmetic Operator)

- `+` `-` `*` `/` `%` : 덧셈, 뺄셈, 곱셈, 나눗셈, 나머지

```jsx
console.log(1 + 2); // 덧셈, 3
console.log(5 - 7); // 뺄셈, 2
console.log(3 * 4); // 곱, 12
console.log(10 / 2); // 나눗셈, 5
console.log(7 % 5); // 나머지, 2
```

## 할당 연산자(Assignment Operator)

```jsx
a = a + 1; // a += 1
a = a - 1; // a -= 1
a = a * 1; // a *= 1
a = a / 1; // a /= 1
```

## 비교 연산자(Comparison Operator)

- `===` : 일치(type + 값)하면 true
- `!==` : 일치(type + 값)하지 않으면 true
- `>, <, <=, >=` : 초과, 미만, 이상, 이하

```jsx
const a = 1
const b = 1
console.log(a === b) // true, 일치 연산자(데이터 타입 + 값)

const isEqual(x, y) {
	return x === y
}

console.log(isEqual(1, 1)) // true
console.log(isEqual(2, '2')) // false

const a = 1
const b = 3
console.log(a !== b) // true

console.log(a < b) // true
console.log(a > b) // false

const a = 13
const b = 13
console.log(a <= b) // true
console.log(a >= b) // true
```

## 논리 연산자(Logical Operator)

- `&&` : 모두 true이면 true
- `||` : 하나라도 true이면 true
- `!` : true이면 false, false으면 true

```jsx
const a = 1 === 1;
const b = "AB" === "AB";
const c = false;

console.log(a); // true
console.log(b); // true
console.log(c); // false

console.log("&& ", a && c); // false
console.log("||: ", a || b || c); // true
```

## 삼항 연산자(Ternary Operator)

- `condition ? true : false`

```jsx
const a = 1 < 2; // true

if (a) {
  console.log("참"); // ✅
} else {
  console.log("거짓");
}

console.log(a ? "참" : "거짓"); // '참'
```

<br />
<br />
<br />

# 2. 데이터 타입

## 형변환(Type conversion)

- 일치 연산자(`===`): 타입과 값이 일치하는지를 확인힌다. 사용하는 것을 권장한다.
- 동등 연산자(`==`): 형변환이 일어난다. 사용하지 않는 것을 권장한다.

```jsx
const a = 1;
const b = "1";

console.log(a === b); // false
console.log(a == b); // true
```

## Truthy, Falsy

- **Truthy:** `true`, `{}`, `[]`, `1`, `2`, `‘false’`, `-12`, `‘3.14’`
- **Falsy:** `false`, `‘’`, `null`, `undefined`, `0`, `-0`, `NaN`

```jsx
if (true) {
  console.log(123);
}

if ("false") {
  // 문자 => Truthy
  console.log(123);
}
```

<br />
<br />
<br />

# 3. 함수

## arguments 객체

```jsx
function sum() {
  console.log(arguments);
  return;
}

console.log(sum(7, 3));
```

<img src="https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fe00e5f78-9551-4f32-b2de-faae931bdb98%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-05-05_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_4.16.41.png?id=5c8538a9-311a-456a-aeb1-696834782825&table=block" width="300">

- arguments 객체를 사용하면 어떤 인수가 전달되어있는지 확인 가능하다.

## 화살표 함수(arrow function)

```jsx
const doubleArrow = (x) => {
  return x * 2;
};

const doubleArrow = (x) => x * 2;

const doubleArrow = x({ name: "byeonghyeon" });
```

- 매개변수가 하나일 경우 소괄호를 생략할 수 있다.
- return문만 있을 경우 중괄호를 생략할 수 있다.
- 축약형으로 객체를 반환해야하는 경우 소괄호를 써 return한다.

## 즉시실행함수(IIFE)

```jsx
const a = 7(function () {
  console.log(a * 2);
})();
```

- 익명함수를 만들고 소괄호로 함수를 즉시 실행할 수 있다.

## 호이스팅(Hoisting)

함수 선언부가 유효범위 최상단으로 끌어 올려지는 것 처럼 보이는 현상을 의미한다.

```jsx
const a = 7;

double(); // 14

function double() {
  console.log(a * 2);
}
```

- 함수 선언식으로 작성하면 호이스팅이 발생한다.
- 호이스팅을 활용하여 함수 선언을 코드 하단에 작성하고, 함수 호출은 상단에 위치시켜 불필요한 함수의 로직에 대한 세부 내용을 피할 수 있다.

<br />
<br />
<br />

# 4. 클래스

## 생성자 함수(prototype)

```jsx
// 생성자 함수
function User(first, last) {
  this.firstName = first;
  this.lastName = last;
}

// prototype 객체를 통한 메모리 관리
User.prototype.getFullName = function () {
  return `${this.firstName} ${this.lastName}`;
};

// 인스턴스 생성
const heropy = new User("Heropy", "Park");
const amy = new User("Amy", " Clarke");
const neo = new User("Neo", "Smith");
```

- 생성자 함수를 사용하여 객체 데이터를 생성할 수 있고 이를 인스턴스라고 한다.
- 리터럴방식으로 생성자 함수를 만들 필요 없이 간편하게 객체 데이터를 생성할 수 있다.
- getFullName과 같은 동일한 로직은 인스턴스 메모리 효율이 떨어지므로 prototype을 통해 메모리에 한번만 만들어지고 참조하는 방식으로 관리할 수 있다.

## ES6 클래스 패턴

```jsx
class User {
  // 생성자 함수
  constructor(first, last) {
    this.firstName = first;
    this.lastName = last;
  }

  // prototype method 정의
  getFullName() {
    return `${this.first} ${this.lastName}`;
  }
}
```

- constructor를 통해 생성자 함수를 정의할 수 있다.
- protype 함수는 class 내부에 함수를 정의하면 된다.

## 상속(extends)

```jsx
class Vehicle {
  constructor(name, wheel) {
    this.name = name;
    this.wheel = wheel;
  }
}

class Bicycle extends Vehicle {
  constructor(name, wheel) {
    super(name, wheel);
  }
}

class Car extends Vehicle {
  constructor(name, wheel, license) {
    super(name, wheel);
    this.license = license;
  }
}
```

- 상속을 통해 부모 클래스의 기능과 속성을 자식 클래스에서 재사용할 수 있다.
- `super()` 함수를 사용하여 자식 클래스의 생성자에서 부모 클래스의 생성자를 호출할 수 있다.

<br />
<br />
<br />

# 5. 데이터

## 데이터 불변성(immutable)

### 원시 데이터

- `String`, `Number`, `Boolean`, `undefined`, `null`
- 불변하기 때문에 데이터의 생김새가 동일하면 같은 데이터로 취급한다.

### 참조 데이터

- `Object`, `Array`, `Function`
- 가변하기 때문에 새로운 메모리 주소에 할당한다.
- 동일한 데이터가 아닐 수 있으므로 동시 수정이 되어질 수 있다.
- 동시 수정으로 인한 참조 데이터의 할당은 복사 개념이 필요하다.
  복사는 얕은 복사(shallow copy)와 깊은 복사(deep copy)가 있다.

## 복사

### 얕은 복사(shallow copy)

새로운 참조 데이터가 새로운 메모리 주소에 할당한다.

- `Object.assian()` 방식

  ```jsx
  const user = {
    name: "Heropy",
    age: 85,
    emails: ["thesecon@gmail.com"],
  };

  const copyUser = Object.assign({}, user);

  console.log(user === copyUser); // false
  user.age = 22;

  console.log("user", user); // age: 22
  console.log("copyUser", copyUser); // age: 85
  ```

- spread 방식

  ```jsx
  const user = {
    name: 'Heropy',
    age: 85,
    emails: ['thesecon@gmail.com'],
  };

  **const copyUser = {...user}**

  console.log(user === copyUser); // false
  user.age = 22;

  console.log('user', user); // age: 22
  console.log('copyUser', copyUser); // age: 85
  ```

### 깊은 복사(deep copy)

데이터 내부에 참조 데이터가 있을 경우 해당 데이터는 같은 메모리를 참조하기 때문에 깊은 복사가 필요하다.

```jsx
const user = {
  name: "Heropy",
  age: 85,
  emails: ["thesecon@gmail.com"],
};

const copyUser = { ...user };

console.log(user === copyUser); // false
user.age = 22;

user.emails.push("neo@zillinks.com");
console.log(user.emails === copyUser.emails); // ⚠️ true
```

- 전개 방식을 통해 user의 데이터를 얕은 복사한 후 `copyUser`에 이메일에 새로운 이메일을 추가한 이후 `user`와 `copyUser`의 이메일이 같은지를 확인해본다.
- `true`라는 값이 나온 이유는, `email`이 참조 데이터이기 때문에 같은 메모리를 공유하고 있어, 얕은 복사만으로는 해당 배열 값을 복사하지 못하기 때문이다. 때문에 내부의 참조 데이터까지 복사하려면 깊은 복사가 필요하다.

```jsx
import _ from "lodash";

const user = {
  name: "Heropy",
  age: 85,
  emails: ["thesecon@gmail.com"],
};

const copyUser = _.cloneDeep(user);

console.log(user === copyUser); // false
user.age = 22;

console.log("user", user); // age: 22
console.log("copyUser", copyUser); // age: 85

user.emails.push("neo@zillinks.com");
console.log(user.emails === copyUser.emails); // false
```

- 깊은 복사를 직접적으로 구현하기에는 한계가 있어, loadash의 `_.cloneDeep()` 메서드를 통해 딥카피를 진행한다.
- `user`와 `copyUser`의 이메일은 다른 데이터를 갖기 때문에 `false`가 나온다는 것을 확인할 수 있다.
