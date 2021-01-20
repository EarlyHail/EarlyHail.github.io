---
layout: post
title: 왜 response.json()을 비동기 처리해야 할까?
date: 2021-01-20 17:00:00 +0900
author: EarlyHail
categories: Frontend
description: 왜 response.json()을 비동기 처리해야 할까
tags: [Frontend, Network, TCP]
---

## 왜 response.json()을 비동기 처리 해줘야 할까

어제 API 테스트 관련 포스트의 코드를 작성하다가 API 응답에 json()처리를 까먹어서 삽질을 했었다.

```javascript
const response = await fetch(url, headerObj);
const data = await response.json();
```

오늘 아침 스터디에서 이 이야기를 하다가 왜 `json()`도 비동기 처리를 해줘야 하는지에 대해 이야기가 나왔다.

### json()

"Body mixin의 json() 매서드는 Response 스트림을 가져와 스트림이 완료될때까지 읽는다. 이 메서드는 body 텍스트를 JSON으로 바꾸는 결과로 해결되는 promise를 반환한다." - [MDN](https://developer.mozilla.org/ko/docs/Web/API/Body/json)

![json()](/assets/posts/Frontend/Why-await-json/image1.png)

당연하지만, 위 설명과 사진처럼 `json()`이 Promise를 반환하기 때문에 비동기 처리를 해주어야 한다.

### 왜 비동기 처리를 해야하나

간단하다. Body가 다 도착하지 않은 상태이기 때문에 Body가 다 도착할 때 까지 기다렸다가 결과를 리턴하기 때문에 비동기 처리를 해줘야 한다.

### 왜 Body는 한번에 도착하지 않는가?

항상 모든 데이터를 한번에 전송할 수는 없다.

따라서 TCP는 MSS(Maximum Segment Size, 최대 세그먼트 크기)를 결정하고 데이터를 일정 크기로 나눠서 전송한다.

- 세그먼트는 전송 계층의 메시지 이름이지만 실제 MSS는 애플리케이션 계층 데이터에 대한 최대 크기이다.)

- MSS는 MTU(Maximum Transmission Unit, 최대 전송 단위)에 의해 결정된다.

- 항상 최대 크기로 전송되는게 아니라 `혼잡 제어(Congestion Control)`에 의해 점진적으로 증가한다.

데이터를 나눠서 전송하기 때문에 받을때도 나눠서 받게 된다.

Application은 전송 계층의 버퍼에 순차적으로 도착한 데이터를 하나씩 읽는다. 따라서 데이터의 끝까지 다 읽은 후 json으로 파싱해줘야되기 때문에 비동기 처리를 해줘야 한다!

- 사실 데이터는 순차적으로 도착하지 않지만..... TCP가 순차적으로 버퍼에 넣어주니 Application은 순차적으로 접근만 하면 된다.

이러한 이유로 데이터가 한번에 도착하지 않기 때문에, 다 도착할 때 까지 기다렸다가 값을 읽어줘야 한다!
