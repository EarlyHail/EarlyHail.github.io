---
layout: post
title: TypeScript 유용한 문법들
date: 2021-03-19 22:00:00 +0900
author: EarlyHail
categories: JavaScript
description: TypeScript 타입을 위한 문법 정리
tags: [TypeScript]
---

## 1. optional chaining

옵셔널 체이닝(optional chaining)은 ES2020에 추가되었고 체인의 각 참조가 유효한지 명시적으로 검증하지 않고 객체 체인의 속성을 읽을 수 있다.

참조가 nullish(`null` 또는 `undefined`)일 경우 에러를 발생시키지 않고 `undefined`를 반환한다.

```typescript
interface Users {
  [key: string]: {
    name: string;
  };
}

const getUserName = (users: Users, userId: string) => {
  // return users[userId].name; // 이 리턴문을 사용할 경우 users에 없는 userId 참조시 에러 발생
  return users[userId]?.name;
};

const users: Users = {};
users.earlyhail = { name: "hojin" };

console.log(getUserName(users, "earlyhail")); // hojin
console.log(getUserName(users, "nickname")); // undefined
```

### optional chaining의 transpile

[@babel/plugin-proposal-optional-chaining](https://babeljs.io/docs/en/babel-plugin-proposal-optional-chaining)을 사용하여 폴리픽을 적용할 수 있다.

```typescript
const getUserName = (users: Users, userId: string) => {
  // return users[userId].name; // 이 리턴문을 사용할 경우 users에 없는 userId 참조시 에러 발생
  return users[userId]?.name;
};
```

위 코드는

```typescript
var getUserName = function (users, userId) {
  var user;
  return (user = users[userId]) === null || user === void 0
    ? void 0
    : user.name;
};
```

이렇게 변경된다, 참고로 `void 0 === undefined`는 true이다.

## 2. Null Coalesce

널 병합 연산자 (Null coalescing operator)는 ES2020에 추가되었고 왼쪽 피연산자가 null 또는 undefined일 때 오른쪽 피연산자를 반환하고 그렇지 않으면 왼쪽 피연산자를 반환한다.

```typescript
console.log(null ?? "default string"); // default string
console.log(null || "default string"); // default string

console.log(undefined ?? "default string"); // default string
console.log(undefined || "default string"); // default string

console.log(0 ?? "default string"); // 0
console.log(0 || "default string"); // default string

console.log("" ?? "default string"); // "" empty string
console.log("" || "default string"); // default string

console.log(false ?? "default string"); // false
console.log(false || "default string"); // default string
```

`||`연산자의 문제점은 0과 빈 스트링`""`일 경우 falsy값일 경우로 분기한다.

널 병합 연산자 `??`는 `null`과 `undefined`만 잡아주기 때문에 더 안전하다.

### optional chaining의 transpile

[@babel/plugin-proposal-nullish-coalescing-operator](https://babeljs.io/docs/en/babel-plugin-proposal-nullish-coalescing-operator)을 사용하여 폴리픽을 적용할 수 있다.

```typescript
null ?? "default string";
```

위 코드는

```typescript
console.log(null !== null && null !== void 0 ? null : "default string"); // default string
```

이렇게 변경된다. 좌측 연산자가 null이 아니고 undefined도 아닐 경우만 좌측 연산자를 그대로 리턴하고, 하나라도 같을 경우 오른쪽 연산자를 리턴한다.

## 3. Logical Assignment Operator

Logical Assignment Operator는 ES2021에 추가되었다.

- Logical AND assignment

  `x &&= y`일 때 x가 truthy일 경우에만 x에 y를 할당한다.

  ```typescript
  let a = 1;
  let b = 0;

  a &&= 2;
  console.log(a);
  // expected output: 2

  b &&= 2;
  console.log(b);
  // expected output: 0
  ```

  `&&=` 연산은 Short-circuit evaluation이다. 따라서 a가 false일 경우 뒤 연산은 실행되지 않는다.

  `a &&= 2`는 마치 `a = a && 2`처럼 보이지만 a가 참일 경우 a가 재할당 되지 않는다.

  `a &&= 2`의 transpile 결과는 `a && (a = 2);`이다.

  ```typescript
  let a = 0;
  let b = 2;

  a &&= b++;
  console.log(a); // 0
  console.log(b); // 2
  ```

- Logical OR assignment

  `x ||= y`일 때 x가 falsy일 경우에만 x에 y를 할당한다.

  ```typescript
  const a = { duration: 50, title: "" };

  a.duration ||= 10;
  console.log(a.duration); //

  a.title ||= "title is empty.";
  console.log(a.title);
  ```

Logical AND assignment처럼 Short-circuit evaluation이다. 따라서 a가 false일 경우 뒤 연산은 실행되지 않는다.

`a.duration ||= 10`도 마치 `a.duration = a.duration || 10` 처럼 보이지만 a가 참일 경우 a가 재할당 되지 않는다.

`a.duration ||= 10`의 transpile 결과는 `a.duration || (a.duration = 10)`이다.

### Logical Assignment Operator의 transpile

[@babel/plugin-proposal-logical-assignment-operators](https://babeljs.io/docs/en/babel-plugin-proposal-logical-assignment-operators)을 사용하여 폴리픽을 적용할 수 있다.
