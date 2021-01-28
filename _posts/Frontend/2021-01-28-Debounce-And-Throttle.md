---
layout: post
title: Debounce와 Throttle
date: 2021-01-28 01:00:00 +0900
author: EarlyHail
categories: Frontend
description: Debounc와 Throttle에 대해 알아보고 구현해보자
tags: [Frontend, Debounce, Throttle]
---

## Debounce와 Throttle

Debounce와 Throttle은 시간에 따라 함수가 실행되는 횟수를 조절하는 방법이다.

- 예시 : 자동 저장 기능 구현

  사용자가 페이지에서 문서를 작성할 때 자동으로 서버에 저장 요청을 보내는 기능을 만들어야 한다. 구현 방법은 여러가지가 있을 수 있다.

  1. 사용자가 입력할 때 마다 저장 API 호출

  2. 특정 길이만큼 입력하면 저장 API 호출

  3. 사용자가 입력 후 일정 시간동안 입력하지 않으면 저장 API 호출

  4. 한번 저장 API를 호출하면 일정 시간동안 API를 호출하지 않도록

  **3번**은 여러번의 함수 호출을 그룹화하여 하나로 만드는 `Debounce` 방법이다.

  **4번**은 함수 호출의 주기를 조절하는 방법인 `Throttle`이다.

### Debounce

여러번의 함수 호출을 `그룹화`하여 하나로 만드는 것이다.

1번은 매우 비효율적이기 때문에 사용자가 일정 시간동안 입력하지 않을 때 까지의 입력값을 `그룹화`하여 한번에 전송하도록 만드는 것이다.

마지막 입력 이후 API 전송 Timeout 전에 다시 입력을 시작할 경우 또 일정 시간을 기다린다.

### Throttle

특정 시간동안 한번만 함수가 호출되도록 제한하는 것이다.

1번 또는 2번에서 한번 API를 호출하면 일정 시간동안 API를 호출하지 않도록한다.

예시에서 Debounce를 사용할 경우 사용자가 계속 입력하다가 페이지를 떠날 경우 저장이 안되는 단점을 어느정도 보안할 수 있다.

## 예제로 알아보는 Debounce와 Throttle

### 1. 자동 저장의 Debounce와 Throttle

1. 아무런 처리를 하지 않았을 때

   ```javascript
   // 서버에게 자동 저장 요청을 보내는 함수
   const requestSave = (input) => console.log("Request Send!");

   // 사용자가 SpaceBar를 입력할 경우
   document.querySelector(".user-input").addEventListener("input", (e) => {
     requestSave(e.target.value);
   });
   ```

   ![With-No-Action](/assets/posts/Frontend/Debounce-And-Throttle/img2.gif)

   SpaceBar를 입력할 때만 저장하도록 했기 때문에 요청이 그렇게 많이 일어나지는 않지만 여러번의 SpaceBar가 한번에 입력될 경우 많은 요청이 발생한다.

2. Debounce를 이용

   Debounce는 여러번의 함수 호출을 그룹화 하여 한번에 전송하는 것이다.

   간단하게 스페이스바를 입력한 후 몇초동안 입력이 없으면 전송하도록 구현할 수 있다.

   ```javascript
   // 서버에게 자동 저장 요청을 보내는 함수
   const requestSave = (input) => console.log("Request Send!");

   // 사용자가 SpaceBar를 입력할 경우
   let requestTimer;
   document.querySelector(".user-input").addEventListener("input", (e) => {
     // 타이머를 재시작
     clearTimeout(requestTimer);
     requestTimer = setTimeout(() => {
       requestSave(e.target.value);
     }, 1000);
   });
   ```

   ![Debounce-Example](/assets/posts/Frontend/Debounce-And-Throttle/img3.gif)

3. Throttle을 이용

   Throttle은 **특정 시간동안 한번만 호출**하는 것이므로 flag를 이용하여 간단하게 구현할 수 있다.

   ```javascript
   // 서버에게 자동 저장 요청을 보내는 함수
   const requestSave = (input) => console.log("Request Send!");

   // 사용자가 SpaceBar를 입력할 경우
   let requestFlag = false;
   document.querySelector(".user-input").addEventListener("input", (e) => {
     if (requestFlag == true) return;
     requestSave(e.target.value);
     requestFlag = true;
     setTimeout(() => (requestFlag = false), 1000);
   });
   ```

   ![Throttle-Example](/assets/posts/Frontend/Debounce-And-Throttle/img4.gif)

### 2. Infinite Scroll의 Throttle

1.  아무런 처리를 하지 않았을 때

    ```javascript
    let cardIndex = 0;

    // API 요청을 한다고 가정하기 위한 함수, 0.5초 대기 후 true 반환
    const requestCard = () =>
      new Promise((resolve) => setTimeout(resolve, 500, true));

    // API 요청 (requestCard)후 card를 20개 삽입하는 함수
    const insertTwentyCard = async (idx) => {
      const result = await requestCard();
      if (result === false) return false;
      const cardTemplate = (title, content) =>
        `<div class="card"><h2 class="card-title">${title}</h2><p class="card-content">${content}</p></div>`;
      const elCardContainer = document.querySelector(".card-container");
      for (let i = 0; i <= 20; i++) {
        const targetIdx = idx * 20 + i;
        elCardContainer.insertAdjacentHTML(
          "beforeend",
          cardTemplate("title" + targetIdx, "content" + targetIdx)
        );
      }
      return true;
    };

    // 첫 카드 20개를 렌더링하기 위한 즉시실행함수
    (async () => {
      await insertTwentyCard(cardIndex);
    })();

    // 스크롤이 화면 최하단으로부터 400px 이하에 있을 경우 API 요청 후 카드 삽입
    document.addEventListener("scroll", async () => {
      if (
        window.innerHeight + window.pageYOffset >=
        document.body.scrollHeight - 400
      ) {
        const result = await insertTwentyCard(cardIndex + 1);
        // 삽입이 실패했을 경우 index를 증가시키면 안된다.
        if (result) cardIndex++;
      }
    });
    ```

    ![With-No-Action](/assets/posts/Frontend/Debounce-And-Throttle/img1.gif)

    scroll 이벤트가 여러번 발생하기 때문에 같은 `cardIndex`로 요청을 어려번 보내게 되고 결국 같은 데이터를 중복해서 보여주게 된다.

2.  Throttle을 이용

    ```javascript
    let requestFlag = false;
    document.addEventListener("scroll", async () => {
      if (requestFlag === true) return;
      if (
        window.innerHeight + window.pageYOffset >=
        document.body.scrollHeight - 400
      ) {
        requestFlag = true;
        setTimeout(() => {
          requestFlag = false;
        }, 1000);
        const result = await insertTwentyCard(cardIndex + 1);
        // 삽입이 실패했을 경우 index를 증가시키면 안된다.
        if (result) cardIndex++;
      }
    });
    ```

    특정 시간동안 사용자가 스크롤을 맨 밑으로 내려버릴 경우 새로운 데이터가 보여지지 않는 이슈

    API 요청에 걸리는 시간보다 대기 시간이 길어질 경우 같은 index에 대한 요청을 다시 보내는 이슈가 있다.

    전자의 경우 삽입 후 위치를 체크해주는 방법, 후자의 경우 삽입 전 index를 확인하여 해결할 수 있다.
