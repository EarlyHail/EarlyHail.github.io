---
layout: post
title: Strict Mode
date: 2021-01-21 17:00:00 +0900
author: EarlyHail
categories: JavaScript
description: JavaScript Strict Mode에 대해 알아보자
tags: JavaScript
---

## Strict Mode란?

Strict Mode(엄격 모드)는 ES5에 추가된 키워드이다.

브라우저가 코드를 엄격하게 해석하도록 만들어 준다.

- 무시되던 에러들을 throwing

- 엔진 최적화를 방해하는 문제들을 바로잡는다.

  따라서 가끔씩 Strict Mode는 더 빨리 작동할 수 있다.

- ECMAScript의 차기 버전에서 정의될 문법을 금지한다.

### Strict Mode 적용하기

`"use strict";`구문을 삽입하면 Strict Mode를 적용할 수 있다.

- 전체 파일에 script mode 적용

  ```javascript
  "use strict";
  // do something...

  ```

- 함수에 strict mode 적용

  ```javascript
  function strict() {
    "use strict";
    // do something...
  }
  ```

## Strict Mode로 인해 바뀌는 것들

### 1. 허용되던 에러 throw

- `실수로` 글로벌 변수를 생성하지 못하도록

  ```javascript
  num = 10;
  console.log(10);
  ```

  위 코드는 에러를 발생시키지 않는다. 하지만 strict mode에서는 에러가 발생한다.

  ![GlobalVar](/assets/posts/JavaScript/Strict-Mode/img1.png)

- 엄격 모드는 봐주던 에러를 발생시킨다.

  ```javascript
  "use strict";
  // 쓸 수 없는 프로퍼티에 할당
  var undefined = 5; // TypeError 발생
  var Infinity = 5; // TypeError 발생

  // 쓸 수 없는 프로퍼티에 할당
  var obj1 = {};
  Object.defineProperty(obj1, "x", { value: 42, writable: false }); // 쓸 수 없도록 설정
  obj1.x = 9; // TypeError 발생

  // getter-only 프로퍼티에 할당
  var obj2 = {
    get x() {
      return 17;
    },
  };
  obj2.x = 5; // TypeError 발생

  // 확장 불가 객체에 새 프로퍼티 할당
  var fixed = {};
  Object.preventExtensions(fixed);
  fixed.newProp = "ohai"; // TypeError 발생

  // 중복된 이름의 인자
  function duplicated(a, a) {
    console.log(a); // Syntax Error 발생, 원래는 10 출력
  }
  duplicated(5, 10);

  // 수 앞에 0을 사용할 수 없다. (8진수 문법과 헷갈림)
  const sum = 01 + 20 + 30; // Syntax Error 발생
  ```

### 2. 자바스크립트의 보안 향상

JavaScript의 XSS는 보안상 중요한 이슈이다. 사용자가 제출한 코드를 실행시키는 환경이라면 보안 검사는 필수적이다.

엄격 모드는 특정 방식으로 호출되기 때문에 보안 검사의 필요성이 줄어든다.

- this에 전역객체가 노출되지 않는다.

  ```javascript
  "use strict";
  function func() {
    return this;
  }
  console.log(func()); // undefined, 엄격 모드 아닌 경우 window 객체
  ```

- 함수의 호출 변수에 대한 접근을 제공하지 않는다.

  ```javascript
  "use strict";
  function func(a, b, c) {
    console.log(func.arguments);
    console.log(func.caller);
  }
  function caller() {
    func(1, 2, 3);
  }
  caller();
  ```

  ![GlobalVar](/assets/posts/JavaScript/Strict-Mode/img2.png)

  strict mode가 아닌 경우 arguments와 caller를 확인할 수 있다.

사용할만한 것들만 [MDN Strict Mode](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Strict_mode) 를 읽으며 정리해봤다.

Strict Mode는 지원하는 브라우저도 있고, 부분적으로 지원하기도 하고 지원을 하지 않기도 한다. 따라서 Strict Mode를 사용하고 의존해도 되는지 파악 후 사용해야 한다!
