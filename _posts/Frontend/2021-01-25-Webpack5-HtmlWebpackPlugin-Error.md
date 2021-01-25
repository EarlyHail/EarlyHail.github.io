---
layout: post
title: Webpack5 HtmlWebpackPlugin 버전 에러
date: 2021-01-25 23:00:00 +0900
author: EarlyHail
categories: Frontend
description: TypeError The 'compilation' argument must be an instance of Compilation
tags: [Frontend, Webpack]
---

### The 'compilation' argument must be an instance of Compilation 에러

Webpack 설정을 하다가 `The 'compilation' argument must be an instance of Compilation`에러때문에 엄청 삽질을 했다.

```
ERROR in   TypeError: The 'compilation' argument must be an instance of Compilation

  - JavascriptModulesPlugin.js:119 getCompilationHooks
    [frontend]/[webpack]/lib/javascript/JavascriptModulesPlugin.js:119:10

  - CommonJsChunkFormatPlugin.js:30
    [frontend]/[webpack]/lib/javascript/CommonJsChunkFormatPlugin.js:30:19


  - Hook.js:14 Hook.CALL_DELEGATE [as _call]
    [frontend]/[webpack]/[tapable]/lib/Hook.js:14:14

  - Compiler.js:992 Compiler.newCompilation
    [frontend]/[webpack]/lib/Compiler.js:992:30

  - Compiler.js:1035
    [frontend]/[webpack]/lib/Compiler.js:1035:29


  - Hook.js:18 Hook.CALL_ASYNC_DELEGATE [as _callAsync]
    [frontend]/[webpack]/[tapable]/lib/Hook.js:18:14

  - Compiler.js:1030 Compiler.compile
    [frontend]/[webpack]/lib/Compiler.js:1030:28

  - Compiler.js:497 Compiler.runAsChild
    [frontend]/[webpack]/lib/Compiler.js:497:8
```

에러의 이슈는 webpack5와 html-webpack-plugin 간의 버전 차이였다....

![html-webpack-plugin-npm](/assets/posts/Frontend/Webpack5-HtmlWebpackPlugin-Error/img1.png)

![html-webpack-plugin-npm-version](/assets/posts/Frontend/Webpack5-HtmlWebpackPlugin-Error/img2.png)

[html-webpack-plugin npm 사이트](https://www.npmjs.com/package/html-webpack-plugin)에서 Webpack5는 @next 버전을 설치하라고 명시되어 있다.

```
npm uninstall html-webpack-plugin
npm i -D html-webpack-plugin@next
```

로 next 버전을 설치해주면 잘 동작한다!
