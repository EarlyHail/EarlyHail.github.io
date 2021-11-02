---
layout: post
title: TypeScript 상대경로와 비 상대경로
date: 2021-03-10 16:00:00 +0900
author: EarlyHail
categories: JavaScript
description:
tags: [TypeScript]
---

최근 TypeScript 경로 문제때문에 많은 삽질을 했는데 [TypeScript Doc](https://www.typescriptlang.org/docs/handbook/module-resolution.html)에 경로 내용이 나와있어서 정리해보았다.

## Module Resolution?

**Module Resolution**은 compiler가 `import`가 참조하는 것이 무엇인지 알아내기 위한 작업이다.

`import {a} from "moduleA"`를 사용할 때, `a`를 사용하기 위해서 `moduleA`에서 a의 정의를 찾아야 한다.

moduleA에 대한 타입 정보는 `.ts`파일이나 `d.ts`파일을 통해 정의되 있을 것이다.

## 상대경로 vs 비 상대경로 module import

Module의 import는 상대 경로 또는 비 상대경로에 따라 다르게 해석된다.

- 상대경로

  - `import fetchUtil from "../util/fetchUtil`

  - `import fetchUtil from "/src/util/fetchUtil`

  상대경로 import는 **import문을 사용한 파일을 기준**으로 해석된다. 따라서 declaration file이 ambient module (하나의 큰 d.ts 파일)일 경우 모듈 선언을 resolve할 수 없다.

  상대경로 import는 동일한 모듈 내에서 사용하여 상대적 위치를 보장해주어야 한다.

- 절대경로

  - `import axios from "axios"`

  - `import fetchUtil from "@util/fetchUtil"`

  절대경로 import는 `baseUrl`을 기반으로 경로를 해석한다. 따라서 ambient module 선언을 적절하게 해석할 수 있다.

[TypeScript Doc](https://www.typescriptlang.org/docs/handbook/module-resolution.html)에는 ambient module에 대한 설명만이 나와있다.

ambient module 을 사용하지 않으면 크게 상관이 없는것 같지만 개인적으로는 같은 모듈일 때는 상대경로, 다른 모듈을 때는 절대경로를 사용하는게 좋은것 같다

같은 모듈 내에서 절대경로를 사용할 경우 개발을 하는데 문제는 없지만 `d.ts`파일을 생성할 경우 import 경로가 상대 경로로 resolve되지 않고 절대경로로 출력된다.

물론 절대경로를 상대경로로 바꾸는 방법이 없는건 아니지만 tsc(TypeScript Compiler)가 지원하지 않으므로 많은 삽질이 필요할 거다. (며칠동안 삽질하다가 포기한적이 있다......)
