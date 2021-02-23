---
layout: post
title: 이미지 비콘을 통한 정보 보내기
date: 2021-02-23 22:00:00 +0900
author: EarlyHail
categories: Frontend
description: 이미지 비콘을 이용하여 정보를 보내보자
tags: Frontend
---

## 웹 비콘

document에 이미지를 삽입하여 정보를 전송하는 방법이다.

이미지를 삽입과 정보 전송은 엄청 안어울릴것 같은데.... 아래 img 태그를 한번 봐보자.

```html
<img src="http://localhost:3000/visits/?postNum=5" />
```

이미지 태그를 삽입하고 사용자가 방문하게 되면 localhost:3000의 visits주소로 GET 요청을 보낸다.

이를 통해 서버는 postNum을 쿼리로 받아 적절한 동작을 수행할 수 있다.

img GET 요청을 이용하여 POST처럼 동작하도록 만드는 것이다.

### JavaScript와 웹 비콘을 이용하여 정보 전송하기

```javascript
const elImg = document.createElement("img");
elImg.src = "http://localhost:3000/?u=earlyhail&code=101";
document.body.appendChild(elImg);
```

이처럼 간단하게 img를 추가해주며 정보를 전송할 수 있다.

웹 비콘 방법은 JavaScript가 동작하지 않는 환경에서 정보를 전송하는데에 사용할 수도 있고

CORS정책에 의해 POST요청을 보내지 못할 때 대용으로 사용할 수도 있다.
