---
layout: post
title: Koa vs Express
date: 2021-02-04 01:30:00 +0900
author: EarlyHail
categories: Etc
description: Koa와 Express의 차이점
tags: [Backend, Nodejs, Koa, Express]
---

최근 프로젝트에서 Express 대신 Koa를 사용했다.

Koa를 사용한 이유는 너무 Express만 써서 새로운 프레임워크를 사용해보고 싶어서 + Express의 비동기 처리가 귀찮아서 였다.

비슷하지만 다른 Koa와 Express에 대해 알아보자

### 문법적 차이점

- Express

  ```javascript
  router.get("/", (req, res, next) => {
    res.json({ framework: "Express" });
  });
  ```

- Koa

  ```javascript
  router.get("/", (ctx, next) => {
    ctx.body = { framework: "Koa" };
  });
  ```

Koa는 request와 response 객체를 Context 객체로 캡슐화 한다.

Context 객체의 자세한 사용법은 [Koa 홈페이지](https://koajs.com/)에 잘 나와있다.

### 비동기의 차이

- Express

  ```javascript
  app.get("/", () => {
    throw Error();
  });

  app.use((err, req, res, next) => {
    console.log("error Catched!");
    res.status(404);
  });
  ```

  서버의 로그에 `error Catched!`가 출력되는건 당연한것 처럼 보인다.

  하지만 get의 callback이 비동기 함수라면 어떨까?

  ```javascript
  app.get("/", async () => {
    throw Error();
  });

  app.use((err, req, res, next) => {
    console.log("error Catched!");
    res.status(404);
  });
  ```

  ![Express-Async-Error](/assets/posts/Etc/Koa-vs-Express/img1.png)

  에러가 발생하고, 클라이언트는 아무런 응답을 받지 못한다.

  promise reject 에러를 잡아내지 못한다!

- Koa

  ```javascript
  router.get("/error", async (ctx, next) => {
    throw Error();
  });

  app.on("error", (err, ctx) => {
    console.log("error Catched!");
    ctx.status = 404;
  });
  ```

  Koa는 비동기 상황에서 발생한 에러도 잘 잡아내 준다.

### 왜 Express는 Promise Reject를 처리하지 못할까

Koa는 `async / await`을 사용하여 구현되었고 Express는 `callback` 지향적으로 구현되었다.

이는 Express의 코드 주석에서도 확인할 수 있다.

![Express-Async-Example](/assets/posts/Etc/Koa-vs-Express/img2.png)

아마 Error를 제외한 Promise Reject에 대해 고려를 하지 않고 있는것 같다.

### Express의 비동기 문제 해결 방법

1. 비동기 callback 내에서 try-catch로 에러를 잡아서 직접 핸들링 해준다.

2. [express-asyncify](https://www.npmjs.com/package/express-asyncify)를 사용한다.

3. Koa를 사용한다.

---

**Reference**

[koa-vs-express](https://raygun.com/blog/koa-vs-express/)
