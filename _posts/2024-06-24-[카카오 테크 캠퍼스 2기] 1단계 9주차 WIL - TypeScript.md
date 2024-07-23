---
layout: post
title: "[카카오 테크 캠퍼스 2기] 1단계 9주차 WIL - TypeScript"
category: kakao1
---

<br />
<br />
<br />

![image.png](https://velog.velcdn.com/images/sj_yun/post/a63a7617-5c05-4616-b532-ab9b5ad7bbf5/image.png)

# 1. TypeScript란?

TypeScript는 자바스크립트의 상위 집합 언어로, 자바스크립트에 정적 타입을 추가하여 코드의 안전성을 높일 수 있다.

자바스크립트로 컴파일되어 실행되고 TypeScript는 동적 타입 언어인 자바스크립트와 달리 코드 작성 단계에서 타입 오류를 확인할 수 있다.

- 동적 타입: 런타임에서 동작할 때 타입 오류 확인
- 정적 타입: 코드 작성 단계에서 타입 오류 확인, 자바스크립트로 컴파일(Node.js 환경)

<br />
<br />
<br />

# 2. tuple

실제 배열을 사용하는 개념인 건 같지만 아이템의 **개수**와 **각 아이템에 대한 타입**, 그리고 데이터의 **순서**까지 고려하여 지정할 수 있도록 한다.

![스크린샷 2024-06-23 오후 12.45.23.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F9f7a5d83-0e71-4cec-8ac1-81c6850e1222%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-23_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_12.45.23.png?id=bc276a0a-a9a9-4cd6-83ac-18b256d0d83e&table=block)

<br />
<br />
<br />

# 3. 타입 추론(Inference)

타입스크립트는 기본적으로 타입 추론을 하기 때문에 무조건 타입을 정의한다기 보단 타입스크립트가 추론하지 못하는 곳에서만 type을 지정해주면 된다.

1. `초기화된 변수`
2. `기본값이 설정된 매개 변수`
3. `반환 값이 있는 함수`

```tsx
// 1. 초기화된 변수 => 타입 추론 가능
let number = 12;

// 2. 기본 값이 설정된 매개 변수
function addNumber(a: number, b = 2) {
  return a + b;
  // 3. 반환 값이 있는 함수.
  // 숫자 + 숫자 = 숫자 => 추론 가능하므로 number 타입으로 반환된다고 명시하지 않아도 됨.
}
```

<br />
<br />
<br />

# 4. 타입 단언(Aseertion)

- `as`: 특정 타입으로 단언할 때 사용한다.
- Non-null - `!` : null이나 undefined가 아니다 라는 것을 단언할 때 사용한다.

```tsx
const el = document.querySelector("body");
el.textContent = "Hello World"; // error
```

요소를 찾지 못하면 null을 반환하거나 요소를 찾으면 HTMLBodyElement 타입을 정의할 수 있다.

## as 키워드

```tsx
const el = document.querySelector("body") as HTMLBodyElement;
el.textContent = "Hello World";
```

그러나 null을 반환하지 않는다고 확신이 있다면 as 키워드를 통해 단언할 수 있다.

## `!` 키워드

```tsx
const el = document.querySelector("body");
el!.textContent = "Hello World";
```

null을 반환하지 않는다고 확신성이 있다면 `!` 연산자를 통해 단언할 수 있다.

```tsx
function getValue(x: string | number, isNumber: boolean) {
  if (isNumber) {
    return Number(x.toFixed(2));
  }
  return x.toUpperCase();
}

getValue("hello World", false);
getValue(3.141592, true);
```

## `as` 키워드

```tsx
function getValue(x: string | number, isNumber: boolean) {
  if (isNumber) {
    return Number((x as number).toFixed(2));
  }
  return (x as string).toUpperCase();
}

getValue("hello World", false);
getValue(3.141592, true);
```

타입 단언을 남발하는 것은 좋지 않고 type gaurd를 먼저 고려해보고 확신이 들면 단언을 하는 것이 좋다.

## 할당 단언

![스크린샷 2024-06-23 오후 1.26.06.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fdfbec081-52e5-448a-9fe1-f4b4c2a3db59%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-23_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_1.26.06.png?id=b302a1c3-4741-4cd5-a221-c30e875f2944&table=block)

선언을 하고 할당하지 않으면 undefined를 호출한다.

때문에 할당 단언을 통해서 이 변수는 할당을 안한거라고 확신을 주면 에러가 발생하지 않는 것을 확인할 수 있다.

![스크린샷 2024-06-23 오후 1.26.24.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F1ccd795b-c556-44b1-aa4c-921472259ff5%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-23_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_1.26.24.png?id=003f647b-f7ae-4fde-999a-d412b3d745c4&table=block)

<br />
<br />
<br />

# 5. 타입 가드(Type guard)

```tsx
function logText(el: Element) {
  console.log(el.textContent);
}

const h1El = document.querySelector("h1");

logText(h1El); // error 요소를 찾지 못하면 null을 반환하기 때문이다.
```

요소를 찾지 못하면 null을 반환하기 때문에 `as` 키워드를 통해 heading 태그가 나온다는 것을 단언한다.

```tsx
function logText(el: Element) {
  console.log(el.textContent); // error
}

const h1El = document.querySelector("h1") as HTMLHeadingElement;
logText(h1El);
```

그러나 el.textContent에서 에러가 발생한다.

왜냐하면, heading 태그를 받을거라고 생각하였지만, 막상 확인해보니 heading 태그가 아닌 null 값이 logText 함수에 들어왔기 때문이다.

이것은 단언을 잘못한 것이다.

때문에 타입스크립트 코드 자체에서는 에러가 발생하지 않지만 실행시 오류가 발생하는 것이다.

이러한 경우 type guard를 사용해야한다.

```tsx
function logText(el: Element) {
  console.log(el.textContent);
}

const h1El = document.querySelector("h1");

// type gaurd
if (h1El instanceof HTMLHeadingElement) {
  logText(h1El);
}
```

if 조건문을 통해 `h1El`이 truthy(!null)한 값이면 logText 함수를 호출하도록 해서 타입가드를 한다.

```tsx
function getAddNumber(val: string | number | boolean) {
  let res = "Result: ";

  if (typeof val === "number") {
    res += val.toFixed(2);
  }

  if (typeof val === "string") {
    res += val.toUpperCase();
  }
  console.log(res);
}

getAddNumber(3.141592);
getAddNumber("hello World");
```

typeof 를 통해서 val의 type을 확인하고 이에따른 로직을 if 조건문으로 나누어 동작하도록 type guard를 작성할 수도 있다.

<br />
<br />
<br />

# 6. Interface

## 함수 시그니처

getName이라는 함수가 많은 곳에서 사용될 경우 재사용성 있게 타입을 지정할 필요가 있다.

때문에 interface의 함수 시그니처를 통해 함수의 타입을 정의하여 재사용성 있게 사용할 수 있다.

```tsx
interface GetName {
  (message: string): string;
}

interface User {
  name: string;
  age: number;
  getName: **GetName**;
}

const heropy: User = {
  name: 'Heropy',
  age: 1,
  getName(message: string) {
    console.log(message);
    return this.name;
  },
};

heropy.getName('hello~');
```

만약 getName이 한 곳에서만 사용되는 경우 따로 interface의 함수 시그니처를 통해 분리할 필요가 없다.

때문에 User interface에 getName에 대한 함수 타입을 정의할 수 있다.

```tsx
interface User {
  name: string;
  age: number;
  getName: (message: string) => string;
}

const heropy: User = {
  name: "Heropy",
  age: 1,
  getName(message: string) {
    console.log(message);
    return this.name;
  },
};

heropy.getName("hello~");
```

## 인덱스 가능 타입 - 인덱스 시그니처

```tsx
interface Fruits {
  [item: number]: string;
}

const fruits: Fruits = ["Apple", "Banana", "Cherry"];
console.log(fruits[0]);
```

Fruits에 인덱스의 값으로 number가 들어오고 값으론 string을 할당할 수 있도록 타입을 지정할 수 있다.

```tsx
interface User {
  [key: string]: unknown;
  name: string;
  age: number;
}

const heropy: User = {
  name: "heropy",
  age: 12,
};

heropy["isValid"] = true;
heropy["emails"] = ["thesf@gmail.com", "test@gmail.com"];
```

User도 key가 string이고 값은 여러 타입의 값으로 할당 될 수 있기 때문에 unknown으로 지정한다.

이후 해당 객체에 string값을 갖는 key값에 여러 타입의 값을 할당할 수 있도록 할 수 있다.

## 확장(상속)

```tsx
interface UserA {
  name: string;
  age: number;
}

interface UserB extends UserA {
  isValid: boolean;
}

const heropy: UserA = {
  name: "Heropy",
  age: 12,
};

const neo: UserB = {
  name: "Neo",
  age: 12,
  isValid: false,
};
```

`extends` 키워드를 통해 다른 interface를 상속받을 수 있다.

```tsx
interface FullName {
  firstName: string;
  lastName: string;
}

interface FullName {
  middleName: string;
  lastName: boolean;
}
```

interface를 동일한 이름으로 정의 가능하다. 그러나 기존의 interface의 같은 속성의 타입이 같아야 한다.

때문에 다음과 같이 수정할 필요가 있다.

```tsx
interface FullName {
  firstName: string;
  lastName: string;
}

interface FullName {
  middleName: string;
  lastName: string;
}

const fullname: FullName = {
  firstName: "Tomas",
  middleName: "Sean",
  lastName: "Connerys",
};
```

<br />
<br />
<br />

# 7. 타입 별칭(Alias)

단일 타입이나 union 혹은 intersection으로 구성된 타입을 별칭을 통해서 따로 타입을 정의할 수 있다.

```tsx
type TypeA = string;
type TypeB = string | number | boolean;
```

정의된 타입들을 기반으로 함수를 다음과 같이 작성한다고 가정한다.

```tsx
function someFunc(param: TypeB): TypeA {
  switch (typeof param) {
    case "string":
      return param.toUpperCase(); // string
    case "number":
      return param.toFixed(2); // string
    default:
      return false; // error
  }
}
```

매개변수로 받는 param의 타입은 TypeB인 string이거나 number이거나 boolean 값이 들어올 수 있다는 것을 확인할 수 있다.

또한 해당 함수는 반환값으로 TypeA 즉, string값을 반환한다는 것을 확인할 수 있다.

이를 기반으로 로직을 살펴보면 매개변수의 타입이 string이거나 number일 경우 반환값을 정상적으로 string으로 반환하는 것을 확인할 수 있지만 default의 경우 boolean으로, 기존에 정의되지 않은 string과 다른 boolean 값을 반환하기 때문에 error가 발생하는 것을 알 수 있다.

때문에 false가 아닌 ‘False’ 와 같이 문자열 형태로 수정하면 에러가 발생하는 것을 막을 수 있다.

```tsx
function someFunc(param: TypeB): TypeA {
  switch (typeof param) {
    case "string":
      return param.toUpperCase(); // string
    case "number":
      return param.toFixed(2); // string
    default:
      return "False"; // string
  }
}
```

## interface와 type중 어떤 것을 써야할까?

```tsx
type TypeUser = {
  name: string;
  age: number;
  isValid: boolean;
};

interface InterfaceUser {
  name: string;
  age: number;
  isValid: boolean;
}

const heropy: InterfaceUser = {
  name: "heropy",
  age: 12,
  isValid: true,
};

const heropy2: TypeUser = {
  name: "heropy2",
  age: 24,
  isValid: boolean,
};
```

어떤 별칭을 사용해도 상관 없지만 interface같은 경우 바로 객체 데이터의 타입을 지정할 수 있다.

type은 데이터의 타입이 오는 것이 아닌 할당연산자가 들어온다.

그럼 두개중 어떤 방식으로 사용하는게 좋을까?

기능적으로 차이는 없지만, 취향에 맞게 쓸 수 있다. 그러나 굳이 권장을 하자면 interface 방식을 더 권장한다.

명확한 이유는 없지만 type은 다양한 타입의 별칭을 지정하는 용도라서 사용 범위가 넓은 반면, interface는 기본적으로 객체 데이터를 전제하기 때문에 interface가 더 낫을 수 있다.

<br />
<br />
<br />

# 8. 함수

## 명시적 this

```tsx
interface Cat {
  name: string;
  age: number;
}

const cat: Cat = {
  name: "Lucy",
  age: 3,
};

function hello(this: Cat, message: string) {
  console.log(`Hello ${this.name}, ${message}`);
}

hello.call(cat, "You are pretty awesome!");
```

call함수를 통해서 어떤 대상에서 실행할 것인지 결정할 수 있고, 그 대상을 첫번째 인수로 넘길 수 있다.

여기서는 첫번째 인수 즉 this를 cat의 객체 데이터를 넘기는 코드임을 확인할 수 있다.

그러나 this의 타입이 명시 되어있지 않기 때문에 암묵적으로 this의 타입이 any 타입으로 명시되어버린다.

그렇기 때문에 this에 대한 type을 지정해주어 any 타입으로 되지 않도록 해야한다.

## 오버로딩(Overloading)

```tsx
function add(a: string, b: string): string; // 첫번째 함수 타입 선언
function add(a: number, b: number): number; // 두번째 함수 타입 선언
// 위의 두 타입을 오버로딩을 통해 분기로 정의하고 있는 상태이다.

function add(a: any, b: any) {
  // 함수를 구현하는 부분
  return a + b;
}

add("hello ", "world!"); // a: string, b: string => string
add(1, 2); // a: number, b: number => number

add("hello ", 2); // error => 첫, 두번째 에 해당하는 타입이 없기 때문에
add(1, "world"); // error =>  첫, 두번째에 해당하는 타입이 없기 때문에
```

add라는 같은 함수를 2개의 타입으로 선언한 상태이고 아래에 해당 add 메소드의 구현부를 나타낸 것이다.

add에 인자를 전달하고 결과를 확인해 보면 `a: number`, `b: number` 혹은 `a: string`, `b: string`이 아닌 다른 타입이 올 경우 error가 발생하는 것을 확인할 수 있다.

이처럼 함수를 여러개로 만들지 않더라도 같은 이름의 함수의 타입을 함수 구현부 상단에 선언하여 오버로딩을 통해 같은 함수더라도 여러 타입으로 관리할 수 있다.

<br />
<br />
<br />

# 9. 클래스와 접근 제어자

## 접근제어자(Access Modifiers)

- `public`: 어디서나 자유롭게 접근 가능, 클래스 바디에서 **생략** 가능
- `protected`: **자기 자신**와 **후손 클래스**에서만 접근 가능
- `private`: **자기 자신**의 클래스만 접근 가능

this 속성에 해당하는 타입들은 해당 class의 body에 정의하면 된다.

아래의 코드는 this 속성의 접근 제어자가 public일때 해당하는 코드이다.

```tsx
class UserA {
  public first: string = "";
  public last: string = "";
  public age: number = 0;

  constructor(first: string, last: string, age: number) {
    this.first = first;
    this.last = last;
    this.age = age;
  }

  getAge() {
    return `${this.first} ${this.last} is ${this.age}`;
  }
}

class UserB extends UserA {
  getAge() {
    return `${this.first} ${this.last} is ${this.age}`;
  }
}

class UserC extends UserB {
  getAge() {
    return `${this.first} ${this.last} is ${this.age}`;
  }
}

const neo = new UserA("Neo", "Anderson", 102);
console.log(neo.first);
console.log(neo.last);
console.log(neo.age);
```

first와 last, age를 public 접근제어자로 타입을 선언했기 때문에 모든 클래스에서 해당 this 속성들을 접근할 수 있게 된다.

이후 UserA 클래스의 객체를 생성하고 해당 객체의 first와 last, age를 출력하면 잘 출력되는 것을 확인할 수 있다.

다음은 protected 접근제어자로 설정했을 경우의 코드이다.

```tsx
class UserA {
  public first: string = "";
  protected last: string = "";
  public age: number = 0;

  constructor(first: string, last: string, age: number) {
    this.first = first;
    this.last = last;
    this.age = age;
  }

  getAge() {
    return `${this.first} ${this.last} is ${this.age}`;
  }
}

class UserB extends UserA {
  getAge() {
    return `${this.first} ${this.last} is ${this.age}`;
  }
}

class UserC extends UserB {
  getAge() {
    return `${this.first} ${this.last} is ${this.age}`;
  }
}

const neo = new UserA("Neo", "Anderson", 102);
console.log(neo.first);
console.log(neo.last); // error
console.log(neo.age);
```

UserA에서 last 속성에만 protected로 변경한 사례이다.

때문에 파생된 클래스에서 last를 접근하는 경우에 error가 발생하지 않지만

클래스 외부에서 last를 접근하려고 하면 error가 발생하는 것을 확인할 수 있다.

다음은 private 접근제어자로 설정했을 경우의 코드이다.

```tsx
class UserA {
  public first: string = "";
  protected last: string = "";
  private age: number = 0;

  constructor(first: string, last: string, age: number) {
    this.first = first;
    this.last = last;
    this.age = age;
  }

  protected getAge() {
    return `${this.first} ${this.last} is ${this.age}`;
  }
}

class UserB extends UserA {
  getAge() {
    return `${this.first} ${this.last} is ${this.age}`; // this.age => error
  }
}

class UserC extends UserB {
  getAge() {
    return `${this.first} ${this.last} is ${this.age}`; // this.age => error
  }
}

const neo = new UserA("Neo", "Anderson", 102);
console.log(neo.first);
console.log(neo.last); // error => UserA) protected this.last
console.log(neo.age); // error => UserA) private this.age
neo.getAge(); // error => UserA) protected getAge()
```

private은 자신의 클래스 내부에서만 접근이 가능하기 때문에 파생 클래스에서는 접근이 불가능하다

때문에 UserB와 UserC에서 접근하려고 하는 this.age는 접근이 불가능하므로 error가 발생한다.

또한 외부에서 객체를 생성한 후 last는 protected 접근제어자로 설정하였기 때문에 파생 클래스에서만 접근이 가능하므로 외부에서 `neo.last`를 접근할 경우 error가 발생하게 되고,

`neo.age`에 접근할때도 클래스 내부에서만 접근할 수 있는 접근제어자 private으로 설정하였기 때문에 클래스 외부에 해당하여 error 발생하는 것을 알 수 있다.

마지막으로 getAge는 UserA클래스에서 protected로 접근제어자를 설정하였기 때문에 자기자신 혹은 파생 클래스에서는 접근할 수 있으나 클래스 외부에서는 접근이 불가능하므로 error가 발생하는 것을 알 수 있다.

추가적으로 다음 코드는 this 속성을 초기화 하는 부분을 다음과 같이 축약하여 표현이 가능하다.

```tsx
class UserA {
  constructor(public first: string, public last: string, public age: number) {}

  getAge() {
    return `${this.first} ${this.last} is ${this.age}`;
  }
}

class UserB extends UserA {
  getAge() {
    return `${this.first} ${this.last} is ${this.age}`;
  }
}

class UserC extends UserB {
  getAge() {
    return `${this.first} ${this.last} is ${this.age}`;
  }
}

const neo = new UserA("Neo", "Anderson", 102);
console.log(neo.first);
console.log(neo.last);
console.log(neo.age);
```

매개변수로 받는 first, last, age의 접근 제어자를 설정하고 해당 타입을 선언하면 constructor 내부에서의 초기화 부분을 생략할 수 있다.

그러나 public 같은 경우 클래스 바디 내부에서만 생략이 가능하기 때문에 위와 같이 매개변수를 통해 this 속성을 초기화 할 경우 public 키워드를 생략하면 안된다.

<br />
<br />
<br />

# 10. 제네릭

제네릭을 통해 매개변수로 들어오는 타입 변수를 지정할 수 있고 보통 Pascal Case를 통해 지정한다(주로 T라고 한다.)

## 함수

```tsx
interface Obj {
  x: number;
}
type Arr = [number, number];

function toArray(a: string, b: string): string[];
function toArray(a: number, b: number): number[];
function toArray(a: boolean, b: boolean): boolean[];
function toArray(a: Obj, b: Obj): Obj[];
function toArray(a: Arr, b: Arr): Arr[];

function toArray(a: any, b: any) {
  return [a, b];
}

console.log(
  toArray("Neo", "Anderson"),
  toArray(1, 2),
  toArray(true, false),
  toArray({ x: 1 }, { x: 2 }),
  toArray([1, 2], [3, 4])
);
```

toArray의 함수 타입들을 선언하여 함수의 타입들을 오버로딩하고 선언부 다음 코드에는 toArray 메소드의 구현부를 작성하여 다음과 같이 여러 타입들을 선언할 수 있다.

하지만 제네릭을 사용하면 더 축약해서 타입들을 선언할 수 있다.

```tsx
interface Obj {
  x: number;
}
type Arr = [number, number];

function toArray<T>(a: T, b: T) {
  return [a, b];
}

console.log(
  // toArray<string>('Neo', 'Anderson'),
  toArray('Neo', 'Anderson'),
  // toArray<number>(1, 2),
  toArray(1, 2),
  // toArray<boolean>(true, false),
  toArray(true, false),
  // toArray<Obj>({ x: 1 }, { x: 2 }),
  toArray({ x: 1 }, { x: 2 }),
  // toArray<Arr>([1, 2], [3, 4]),
  toArray([1, 2], [3, 4]),
  toArray([1, 2], ['hello', 'world']) // error => [string, string]
  // toArray([1, 2], [1, 2, 3]) // number[] => 우리가 원하는 타입이 아니기 때문에 이럴때는 타입 추론이 아닌 명시적으로 제네릭을 통해 타입을 전달해야한다.
  toArray<Arr>([1, 2], [1, 2, 3]) // error => Arr
);
```

제네릭 문법을 통해 전달한 인자의 타입들을 보낼 수 있고 전달받은 타입을 가지고 매개변수의 타입을 정의할 수 있다.

해당 코드를 보면 인자로 전달한 a, b의 타입이 모두 같게 설정할 수 있다.

때문에 전달한 인자 두개가 서로 다른 타입을 갖게 되면 에러가 발생할 수 있다는 것을 예상할 수 있다.

또한 함수 호출시 명시적으로 제네릭을 통해 타입을 전달할 수 있지만 타입 추론이 가능하다면 굳이 작성안해도 상관없다.

하지만 타입 추론이 가능하다면 최대한 쓰지 않고 작성하는 것을 권장한다.

예외를 살펴보면 toArray에 인자에 item의 개수가 다른 number[] 타입을 전달하면 원하는 결과를 얻을 수 없다.

이처럼 타입추론을 통해 다른 결과값이 반환된다면 이럴때는 제네릭을 통해 타입을 전달해주어야 한다.

## 클래스

```tsx
class User<P> {
  constructor(public payload: P) {}

  getPayload() {
    return this.payload;
  }
}

interface UserAType {
  name: string;
  age: number;
  isValid: boolean;
}

interface UserBType {
  name: string;
  age: number;
  emails: string[];
}

const heropy = new User<UserAType>({
  name: "Heropy",
  age: 85,
  isValid: true,
});

const neo = new User<UserBType>({
  name: "Neo",
  age: 23,
  emails: ["neo@gmail.com"],
});
```

클래스 뒤에 제네릭을 통해 타입을 정의할 수 있다.

이후 객체를 생성할 때 보내려는 인자의 타입을 제네릭을 통해 클래스에 전달하면 그에 맞는 타입의 값을 설정할 수 있다

## 인터페이스

```tsx
interface MyData<T> {
  name: string;
  value: T;
}

const dataA: MyData<string> = {
  name: "Data A",
  value: "Hello World",
};

const dataB: MyData<number> = {
  name: "Data B",
  value: 3,
};

const dataC: MyData<boolean> = {
  name: "Data C",
  value: false,
};

const dataD: MyData<number[]> = {
  name: "Data D",
  value: [1, 2, 3, 4],
};
```

interface 뒤에 제네릭을 통해 타입 명을 정의하고 제네릭을 통해 들어온 타입을 특정 속성의 타입으로 설정하도록 정의할 수 있다.

이후 서로다른 value의 타입을 가진 객체 data를 선언하기 위해 MyData뒤에 제네릭을 통해 value에 해당하는 타입을 전달하여 원하는 value의 타입을 가진 객체 데이터를 할당할 수 있다.

## 제약조건(Constraints)

```tsx
interface MyData<T extends string | number> {
  name: string;
  value: T;
}

const dataA: MyData<string> = {
  name: "Data A",
  value: "Hello World",
};

const dataB: MyData<number> = {
  name: "Data B",
  value: 3,
};

const dataC: MyData<boolean> = {
  // error
  name: "Data C",
  value: false,
};

const dataD: MyData<number[]> = {
  // error
  name: "Data D",
  value: [1, 2, 3, 4],
};
```

extends 키워드를 통해 타입의 제약조건을 추가할 수 있다.

<br />
<br />
<br />

# 11. 패키지의 타입 선언

lodash 패키지를 예를 들어 진행한다.

lodash 패키지 설치를 위해 다음 명령어를 실행한다.

`npm install lodash`

이후 lodash에 있는 camelCase(), snakeCase(), kebabCase()를 사용하기 위해 다음과 같이 작성한다.

```tsx
import _ from "lodash";

const str = "the brown fox jumps over the lazy dog.";

console.log(_.camelCase(str));
console.log(_.snakeCase(str));
console.log(_.kebabCase(str));
```

하지만 lodash를 가져오는 과정에서 error가 발생하는 것을 알 수 있다.

왜냐하면 lodash에 있는 각 함수에 대한 타입이 정의 되어있지 않기 때문이다. 때문에 타입 정의를 위한 d.ts 파일을 생성해야한다.

lodash.d.ts라는 이름으로 파일을 생성하고 각 함수에 대한 타입을 interface로 다음과 같이 정의할 수 있다.

```tsx
declare module "lodash" {
  interface Lodash {
    camelCase: (str: string) => string;
    snakeCase: (str: string) => string;
    kebabCase: (str: string) => string;
  }
  const _: Lodash;
  export default _;
}
```

interface를 통해 Lodash라는 이름으로 사용하려는 함수에 대한 타입을 정의하고 기본 \_변수에 interface로 정의한 타입을 설정한다.

이후 \_를 export default하여 사용할 수 있도록 정의할 수 있다.

그러면 lodash를 가져오는 과정에서 error가 사라지는 것을 알 수 있다.

그러면 꼭 lodash.d.ts라고 파일명을 지정해야되는지에 대해 의문이 생길 수 있다.

다른 파일명으로 설정이 가능하지만 이러한 경우 삼중지시자와 참조태그를 사용해야한다.

loadash.d.ts를 main.d.ts로 변경한 후 lodash 모듈을 import하기전에 다음과 같이 작성한다.

```tsx
/// <reference path="main.d.ts"/>
import _ from "lodash";

const str = "the brown fox jumps over the lazy dog.";

console.log(_.camelCase(str));
console.log(_.snakeCase(str));
console.log(_.kebabCase(str));
```

`///`는 삼중지시자를 의미하고 reference태그는 참조태그를 의미한다. 이를 사용하여 lodash 모듈을 가져오기 전에 main.d.ts에 정의된 타입들을 가져온후 lodash module을 import하면 error가 사라지는 것을 확인할 수 있다.

그러면 여기서 또 의문점이 있다.

모든 패키지들은 이렇게 d.ts파일을 생성하여 일일이 타입을 정의해야할까?

그렇지 않다. 이미 많이 사용되어지는 패키지들은 다른 개발자들이 이미 타입들을 정의하여 패키지로 제공하는 경우가 많기 때문이다.

[DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)

해당 레포지토리에 들어가보면 많이 사용하는 패키지들의 타입들은 다른 개발자들이 이미 정의하여 패키지로 만들어 놓는다.

때문에 우리는 해당 패키지가 정의된 타입 패키지를 설치하기만 하면된다.

![스크린샷 2024-06-23 오후 6.48.19.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F5b78526d-73bd-41b2-bbfa-c943d72341d5%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-23_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.48.19.png?id=7aa6ffce-82ad-4233-8999-9ff5b456b4a3&table=block)

이처럼 `npm install —save-dev @types/{packageName}` 명령어를 통해 타입 패키지를 설치하면 따로 우리가 d.ts파일을 만들어 타입을 정의할 필요가 없다.

lodash 패키지도 d.ts파일이 있는지 한번 `npm info —save-dev @types/lodash` 명령어를 통해 확인해보자.

참고로 info명령어는 해당 패키지가 있는지에 대한 여부만 확인하므로 설치되지는 않는다.

![스크린샷 2024-06-23 오후 6.50.54.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F47466158-a6ca-48ec-b3ff-6b6e52d47519%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-23_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.50.54.png?id=b7705f17-f627-4ae9-8b26-8b04b0d558e7&table=block)

패키지 정보가 있는 것이 확인되었으니 `npm install —save-dev @types/lodash`를 통해 설치를 한다.

![스크린샷 2024-06-23 오후 6.52.05.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F23ae3ffd-1dd9-4a5b-9bb8-c97e79153299%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-23_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.52.05.png?id=21c7c1a4-53c7-47ab-adf2-6a1813e6199f&table=block)

![스크린샷 2024-06-23 오후 6.52.31.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2Fb8c294c8-7a28-4127-aa1c-5bb550cb04b9%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-23_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.52.31.png?id=761c2f6e-48e5-473f-8897-a3b93cc043ac&table=block)

![스크린샷 2024-06-23 오후 6.54.01.png](https://www.notion.so/image/https%3A%2F%2Fprod-files-secure.s3.us-west-2.amazonaws.com%2F13897cab-0dd6-431f-b847-04477372a586%2F8bcd3662-95f0-4d69-878b-ffe77c7e0246%2F%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2024-06-23_%25E1%2584%258B%25E1%2585%25A9%25E1%2584%2592%25E1%2585%25AE_6.54.01.png?id=16ec2790-aaee-45c7-9120-db13dd4f7500&table=block)

타입 패키지를 성공적으로 설치할 수 있는 것을 확인할 수 있고 node_modules에 @types 폴더를 확인해보면 lodash에 관련한 d.ts파일들이 설치 된것을 확인할 수 있으며 error또한 사라진 것을 확인할 수 있다.
