---
layout: post
title: Vanilla JS Export 없이 Test하기
date: 2021-02-01 21:00:00 +0900
author: EarlyHail
categories: Frontend
description: Export가 없는 Vanilla JS 함수 Jest로 Test하기
tags: [Frontend, Test, Jest]
---

### 왜 export 없이 Test를...

최근 테스트 코드를 짜다가 export되지 않는 함수를 테스트하려고 삽질을 했었다.

물론 그냥 export하면 되는거 아니야? 라고 생각을 할 수도 있지만

export를 하지 못하는, module을 지원하지 않는 환경에서 있을 수도 있고 export를 붙이는건 테스트를 위해 코드를 수정하는 행위이기 때문에 꺼려지기 때문일 수도 있다.

나같은 경우는 전자 후자 모든 이유에 해당이 됬다.

### 테스트 환경

1.  jest 설치

    새로운 폴더 내부에서

    ```
    npm init
    npm i -D jest
    ```

    로 테스트 환경을 만들어준다.

2.  `package.json` script 변경

    ```json
    "scripts": {
        "test": "jest"
    },
    ```

3.  간단한 js파일 만들기

    `src/index.js`파일을 생성한 뒤 간단한 test함수를 만들어보자. export는 하지 않는다.

    ```javascript
    const testTarget = () => {
      return "TestMe";
    };
    ```

## rewire

### rewire 설치하기

먼저 rewire를 설치해주자. 사용법은 **[npm rewire](https://www.npmjs.com/package/rewire)**이나 **[git rewire](https://github.com/jhnns/rewire)**에서 확인할 수 있다.

```
npm i -D rewire
```

### export 없는 함수 테스트하기

`src/index.js`의 `testTarget()`은 모듈로써 export되지 않는다. 따라서 외부 파일에서 접근할 수가 없다.

하지만 rewire는 `__get__`함수를 통해 해당 함수를 가져올 수 있다.

이를 이용해 테스트 코드를 작성하면

```javascript
const { test, expect } = require("@jest/globals");
const rewire = require("rewire");
const target = rewire("../src/index.js");

describe("index.js 테스트", () => {
  test('testTarget은 "TestMe"를 리턴한다', () => {
    const testTarget = target.__get__("testTarget");
    expect(testTarget()).toBe("TestMe");
  });
});
```

이렇게 `testTarget`을 가져와서 테스트할 수 있다.

### \_\_set\_\_이용하기

`index.js`를 rewire할 때의 실행 환경은 node이다. 따라서 index.js의 실행 환경은 node이다.

![index.js의 global](/assets/posts/Frontend/Testing-without-Export/img1.png)

그렇기 때문에 함수에서 document.addEventListener나 querySelector같은 함수를 접근한다면 에러가 발생한다.

rewire의 `__set__`은 파일에 mock 함수를 삽입해주는 기능을 한다.

이를 확인하기 위해 `index.js`에 2개의 함수를 추가해보자

```javascript
const needGlobalDependency = () => {
  return globalObject.nickname;
};

const getNickname = () => {
  const elSomething = document.querySelector(".nickname");
  return elSomething.textContent;
};
```

테스트 함수 2개도 추가해보자.

```javascript
test('globalObject.nickname은 "EarlyHail"이어야 한다.', () => {
  target.__set__({
    globalObject: {
      nickname: "EarlyHail",
    },
  });
  const needGlobalDependency = target.__get__("needGlobalDependency");
  expect(needGlobalDependency()).toBe("EarlyHail");
});
test("nickName을 잘 가져온다.", () => {
  target.__set__({
    document: {
      querySelector: (selector) => document.querySelector(selector),
    },
  });
  document.body.innerHTML = '<div class="nickname">EarlyHail</div>';
  const getNickname = target.__get__("getNickname");
  expect(getNickname()).toBe("EarlyHail");
});
```

- globalObj를 set하지 않을 경우

  ![without-globalObject](/assets/posts/Frontend/Testing-without-Export/img2.png)

- document.querySelector를 설정하지 않을 경우

  ![without-globalObject](/assets/posts/Frontend/Testing-without-Export/img3.png)

- 테스트 결과

  ![test-result](/assets/posts/Frontend/Testing-without-Export/img4.png)

이렇게 테스트하려는 파일 내에서 필요한 의존성을 `__set__`을 이용해 주입해줄 수 있다.
