---
layout: post
title: webpack optimization 옵션에 대해 알아보자
date: 2021-03-12 20:00:00 +0900
author: EarlyHail
categories: JavaScript
description: webpack optimization 옵션에 대해 알아보자
tags: [JavaScript, Webpack]
---

### webpack의 mode 옵션

webpack의 `mode` 옵션은 production, developement에 따라 자동으로 필요한 플러그인을 추가해준다.

production 모드에서는 `chunks`, `FlagDependencyUsagePlugin`, `FlagIncludedChunksPlugin`, `ModuleConcatenationPlugin`, `NoEmitOnErrorsPlugin` 와 `TerserPlugin`을 적용시켜준다.

TerserPlugin을 통해 기본적인 minify, uglify를 지원하기 때문에 따로 설정하지 않아도 간단하게 이용할 수 있다.

```javascript
module.exports = {
  mode: "production",
};
```

하지만 옵션을 커스텀 하고싶은 경우, optimization 옵션을 사용할 수 있다.

## optimization

### minimize

production 모드의 기본이라고 할 수 있는 minify, uglify를 설정한다.

![minimize](/assets/posts/JavaScript/webpack-optimization/img1.png)

왼쪽 : `minimize: true` / 오른쪽 : `minimize: false`

minimize 옵션은 [Terser](https://github.com/terser/terser)를 사용하는 [TerserPlugin](https://webpack.js.org/plugins/terser-webpack-plugin/)을 기본적으로 사용하고, `minimizer` 옵션을 사용하여 사용할 플러그인을 명시할 수 있다.

### minimizer

기본 minimizer의 설정을 override하여 커스터마이징 하거나, 다른 minimizer를 설정하기 위해 사용된다.

예를들어 terser의 기본 동작은 console.log를 제거하지 않지만, 제거하고 싶을 때

```javascript
const path = require("path");
const TerserPlugin = require("terser-webpack-plugin");

module.exports = {
  mode: "production",
  entry: path.join(__dirname, "src", "index.js"),
  output: {
    path: path.join(__dirname, "dist"),
    filename: "bundle.js",
  },
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin({
        extractComments: true,
        terserOptions: {
          compress: {
            drop_console: true,
          },
        },
      }),
    ],
  },
};
```

위 처럼 설정해줄 수 있다.

### splitChunks

[SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/)을 사용하여 chunk를 나눈다.

기본적으로 webpack은 다음 조건을 가지고 chunk를 나눈다.

- 새로운 chunk가 공유될 수 있거나 node_module에 있는 모듈일 경우

- 새로운 chunk가 20kb보다 클 경우

- 필요할때 로딩하는 최대 chunk의 요청 개수가 30개 이하여야 한다.

- 첫 페이지 로드 시 최대 chunk의 요청 갯수가 30개 이하여야 한다.

splitChunks는 여러곳에서 사용하는 모듈을 분리하여 번들링하는데 사용된다.

[webpack splitChunks DOC](https://webpack.js.org/plugins/split-chunks-plugin/)에서 자세한 옵션을 볼수 있고, 간단한 코드를 split해보겠다.

```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
  getName() {
    return this.name;
  }
  setName(name) {
    this.name = name;
  }
}
```

이 클래스를 `index.js`, `index2.js`에서 import하여 사용하도록 구현하였고

```javascript
const path = require("path");

module.exports = {
  mode: "production",
  entry: {
    index1: path.join(__dirname, "src", "index.js"),
    index2: path.join(__dirname, "src", "index2.js"),
  },
  output: {
    path: path.join(__dirname, "dist"),
    filename: "[name].js",
  },
  optimization: {
    minimize: false,
    splitChunks: {
      chunks: "all",
      minSize: 0,
    },
  },
};
```

**webpack.config.js**는 이렇게 설정해주었다.

minSize를 0으로 설정한 것은 새로운 chunk가 20kb보다 커야되는 조건을 무시하기 위해 적용해주었다.

![without-splitChunks](/assets/posts/JavaScript/webpack-optimization/img2.png)

![splitChunks](/assets/posts/JavaScript/webpack-optimization/img3.png)

Person class가 새로운 파일로 분리되었다.

### emitOnErrors

`컴파일 하는 동안 발생한 에러`에 대해 에러가 발생한 asset을 알려준다.

컴파일 과정에서 에러이기 떄문에 결과 코드에 변화는 없었다.

컴파일 과정에서 에러라고 해서 없는 파일을 import해봤는데 emitOnErrors 설정에 따른 에러 문구도 변화가 없었다.

optimization에 있는 에러 발생 옵션이면 try-catch로 처리하는 것과 같은 기능을 예상했는데... 좀 더 찾아봐야겠다.

### nodeEnv

DefinePlugin을 사용하여 프로젝트 내에서 사용할 수 있는 환경변수를 결정한다.

기본적으로 `mode` 옵션에 따라 "production", "development"로 결정되며 없을 경우 false이다.

### removeAvailableModules

이미 부모 module에 포함되어 있는 모듈을 chunk로부터 삭제할지의 여부를 결정한다.

현재는 production의 default 설정은 true(삭제함) 이지만 webpack의 성능을 저하시켜 이후 버전에서는 production에서 false로 변경된다고 한다.

packaging 성능 보다는 중복 코드를 제거하는게 당연히 더 호율적일거라는 생각이 들지만 커스터마이즈가 가능하니 괜찮을것 같기도 하다...
