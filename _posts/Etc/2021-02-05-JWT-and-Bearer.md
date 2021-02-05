---
layout: post
title: JWT와 Bearer에 대해 알아보자
date: 2021-01-01 22:00:00 +0900
author: EarlyHail
categories: Etc
description: JWT와 Bearer
tags: [JWT, Bearer]
---

## JWT? Bearer Token?

Token을 사용한 인증을 구현하면 JWT와 Bearer를 사용하게 된다.

서버가 발행한 JWT 토큰을 사용하기 위해 헤더에 `Authorization : Bearer JWT`형식으로 토큰을 전달한다.

둘 다 Token으로 불리는데 각각은 무엇이며 무엇을 위해 사용되는 걸까?

## JWT

**JWT(Json Web Token)**은 당사자 간에 정보를 JSON 객체로 안전하게(securely) 전송하기 위한 표준이다.

JWT를 이용하여 암호화 된 정보를 전송하는 용도로 사용할 수도 있지만 `서명된 토큰(Signed tokens)`으로써 활용이 가능하다.

토큰에 서명을 하며 데이터를 전송하며 데이터의 `무결성`을 보장할 수 있다.

따라서 JWT의 사용은 크게 2가지로 나뉜다.

- Authorization

- 정보 교환

### JavaScript에서 JWT 만들기

1.  [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken) 패키지 설치

    ```shell
    npm i jsonwebtoken
    ```

2.  JavaScript로 토큰 encoding, decoding

    ```javascript
    const jwt = require("jsonwebtoken");
    const SECRET = "ThisIsSecretKey";
    const token = jwt.sign(
      {
        nickname: "EarlyHail",
      },
      SECRET,
      {
        expiresIn: "1h",
      }
    );
    console.log(token);
    // eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuaWNrbmFtZSI6IkVhcmx5SGFpbCIsImlhdCI6MTYxMjUxNDQ2OCwiZXhwIjoxNjEyNTE4MDY4fQ.gbWjnO0lES0IZF4kHFri0wc5DjG2oukE-cjBPcfuEj64
    const decodedToken = jwt.verify(token, SECRET);
    console.log(decodedToken);
    // { nickname: 'EarlyHail', iat: 1612514473, exp: 1612518073 }
    ```

### Signed JWT의 구조

암호화를 적용하지 않은 서명된 JWT의 구조를 알아보자.

위에서 만들어본 JWT를 자세히 보면, `.`를 이용하여 총 3 부분으로 나뉘어 있는것을 확인할 수 있다.

1. Header

   헤더는 일반적으로 두 부분으로 나뉘며, 토큰 유형 (JWT)와 Signature Algorithm의 정보가 들어가있다.

   위 정보를 Base64Url로 인코딩한 정보를 삽입한다.

2. Payload

   토큰화한 데이터가 들어가있다. 만들어진 시간, 만료 시간 등의 데이터가 추가로 들어갈 수 있다.

   위 정보를 Base64Url로 인코딩한 정보를 삽입한다.

3. Signature

   Header와 Payload를 각각 base64Url로 인코딩 한 값과 SECRET을 함께 서명 알고리즘 을 적용한 결과값이 들어가있다.

### 민감한 정보를 JWT에 넣으면 안된다!!!

Signed token은 데이터 변조로부터 보호되는 무결성을 보장하지만 기밀성을 보장하지 못한다.

```
token1
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuaWNrbmFtZSI6IkVhcmx5SGFpbCIsImlhdCI6MTYxMjUxOTk2MywiZXhwIjoxNjEyNTIzNTYzfQ.akFIGyO0JUbJ2oJU3n2eM4UBku0Bk6mGsOJijpWDzEY

token2
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuaWNrbmFtZSI6IkVhcmx5SGFpbCIsImlhdCI6MTYxMjUxOTk2MywiZXhwIjoxNjEyNTIzNTYzfQ.sWJ2g0503AnN1pe8mGQT_BAqlXdfYFmxCo56fyuB4mg
```

두 개의 토큰은 서로 다른 SECRET으로 만들어진 토큰이다.

SECRET이 다르기 때문에 Signature를 제외한 Header, Payload의 값이 같은 것을 확인할 수 있다.

위 두 값은 Base64Url로 인코딩 되어있기 때문에 간단하게 해독할 수 있다. 따라서 기본 JWT 토큰에 저장되는 값 자체는 `어떠한 보안적 처리도 되지 않은 상태`이다.

위 이유 때문에 토큰을 브라우저의 localStorage나 cookie에 저장하는 것을 지양해야 한다.

## Bearer
