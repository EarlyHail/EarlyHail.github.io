---
layout: post
title: Polyfill 적용 방법들을 비교해보자
date: 2021-03-08 16:00:00 +0900
author: EarlyHail
categories: JavaScript
description: Polyfill 적용 방법들을 비교해보기
tags: [JavaScript, Webpack, Babel, Polyfill]
---

### 테스트 코드

```javascript
new Promise((resolve) => resolve("resolved"));

Object.assign({ name: "HojinLee" }, { nickname: "EarlyHail" });
```

이 코드를 3가지 방법으로 polyfill해보자.

## 1. @babel/polyfill

`@babel/polyfill`은 전역으로 import하여 사용할 수 도 있고, webpack의 entry로 설정하여 사용할 수도 있다.

참고로 @babel/polyfill은 babel 7.4.0부터 deprecated되었다. ([링크](https://babeljs.io/docs/en/babel-polyfill#docsNav))

**webpack.config.js**

```javascript
const path = require("path");

module.exports = {
  entry: ["@babel/polyfill", "./src/index.js"],
  module: {
    rules: [
      {
        test: /\.(js|jsx|ts|tsx)$/,
        exclude: /node_modules/,
        loader: "babel-loader",
      },
    ],
  },
};
```

### 결과

![@babel/polyfill 결과](/assets/posts/JavaScript/polyfills/img1.png)

엄청나게 많은 코드가 삽입된것을 확인할 수 있다.

@babel/polyfill은 전역 공간에 polyfill을 삽입하기 때문에 사용하지 않거나 필요하지 않는 polyfill도 삽입되고, 전역도 오염된다.

## 2. preset-env에 적용하기

transpiling을 위해 많이 사용하지만, polyfill도 적용할 수 있다.

core-js와 core-js를 사용할 방법을 설정해주며 적용할 수 있다.

**babel.config.json**

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "ie": "11"
        },
        "useBuiltIns": "usage",
        "corejs": "3"
      }
    ]
  ]
}
```

### useBuiltIns

이 옵션은 어떻게 polyfill을 적용할 것인지 명시해주기 위해서 사용된다.

1. false (기본값)

   polyfill을 적용하지 않는다.

2. entry

   core-js를 직접 import해주는 방식일 때 사용하는 옵션

   ```javascript
   import "core-js";
   new Promise((resolve) => resolve(true));
   ```

   core-js를 import했지만 target 환경에 필요한 polyfill만 추가시켜준다.

   **target 환경**이므로 Promise만 삽입되는게 아니라, target에 없는 모든 polyfill이 삽입된다.

   ![useBuiltins entry 결과](/assets/posts/JavaScript/polyfills/img2.png)

   Promise와 Object.assign만 사용했지만 ie11에 존재하지 않는 polyfill을 추가하기 때문에 많은 코드가 삽입되었다.

3. usage

   실제로 사용한 코드에 대해서만 polyfill을 적용시켜준다.

   ![useBuiltins usage 결과](/assets/posts/JavaScript/polyfills/img3.png)

## 3. plugin-transform-runtime

**babel.config.json**

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "ie": "11"
        }
      }
    ]
  ],
  "plugins": [
    [
      "@babel/plugin-transform-runtime",
      {
        "corejs": 3
      }
    ]
  ]
}
```

![useBuiltins usage 결과](/assets/posts/JavaScript/polyfills/img4.png)

결과를 보면 preset-env와 다른 부분이 있는데, 전역 오염을 피하기 위해 `Promise`와 `Object.assign`가 사라진걸 확인할 수 있다.

따라서 polyfill이 적용되어도 개발자 도구에서 사용할 수 없고, node_module은 폴리필 된 함수를 사용할 수 없으므로 babel-loader에 exclude로 node_modules를 추가했다면 에러가 발생할 수 있다.
