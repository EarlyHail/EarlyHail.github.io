---
layout: post
title: Vanilla JavaScript로 라우팅 구현하기
date: 2021-02-17 22:00:00 +0900
author: EarlyHail
categories: Frontend
description: Vanilla JavaScript와 history를 이용하여 라우팅 구현하기
tags: [Frontend, JavaScript]
---

### 템플릿

라우팅을 위한 간단한 Express 서버와 Html 파일을 만들어보자.

```javascript
router.get("/:pageNum", function (req, res, next) {
  const { pageNum } = req.params;
  const targetPage = pageNum < 1 ? 1 : pageNum;
  const pages = Array(10)
    .fill()
    .map((x, idx) => `${targetPage} - ${idx + 1}번 글`);
  res.json(pages);
});
```

```html
<html lang="en">
  <head>
    <title>Document</title>
  </head>

  <body>
    <div class="board"></div>
    <div>
      <span class="before-post">&lt;</span>
      <span class="next-post">&gt;</span>
    </div>
    <script>
      const getTemplate = (str) => `<div>${str}</div>`;
      const renderPosts = (posts) => {
        const elBoard = document.querySelector(".board");
        const postsTemplate = posts.reduce((acc, curr) => {
          return acc + getTemplate(curr);
        }, "");
        elBoard.innerHTML = postsTemplate;
      };

      const requestPage = async (pageNum) => {
        const response = await fetch(`/api/${pageNum}`, {
          method: "GET",
        });
        const data = await response.json();
        return data;
      };

      const PageManager = () => {
        let pageNum = 1;
        const renderNextPage = async () => {
          const pages = await requestPage(pageNum + 1);
          renderPosts(pages);
          pageNum++;
        };
        const renderBeforePage = async () => {
          if (pageNum == 1) return;
          const pages = await requestPage(pageNum - 1);
          renderPosts(pages);
          pageNum--;
        };
        return { renderBeforePage, renderNextPage };
      };
      const pageManager = PageManager();

      (async () => {
        const response = await fetch("/api/1", {
          method: "GET",
        });
        const data = await response.json();
        renderPosts(data);
        document
          .querySelector(".before-post")
          .addEventListener("click", pageManager.renderBeforePage);
        document
          .querySelector(".next-post")
          .addEventListener("click", pageManager.renderNextPage);
      })();
    </script>
  </body>
</html>
```

- 서버

  `/api/:pageNum` API는 페이지 번호에 맞는 간단한 게시글 리스트를 반환해준다.

- 프론트엔드

  `requestPage`는 페이지 번호를 인자로 받아 api 요청 후 데이터를 반환한다.

  `renderPosts`는 반환받은 데이터를 렌더링한다.

  `pageManager`는 페이지 번호를 관리하고, 즉시 실행 함수에 의해 추가된 eventListener의 페이지 변경 callback 함수를 제공한다.

  즉시 실행 함수는 첫 번째 페이지를 요청 후 렌더링 하고, `<`와 `>` 버튼에 클릭 이벤트를 추가한다.

### 페이지 URI 변경하기

먼저 페이지 이동 버튼을 눌렀을 떄 URI를 변경해보자.

pageManager의 각 함수에 history 함수를 추가한다.

```javascript
history.pushState({ page: pageNum }, pageNum, `?page=${pageNum}`);
```

![change-query](/assets/posts/Frontend/JavaScript-Route/img1.gif)

페이지 변경 시 주소창의 query가 변경된다!

### 문제점

뒤로가기나 새로고침 버튼을 눌렀을 때, 게시글이 변하지 않고 URI만 변하는걸 확인할 수 있다.

![back-btn-error](/assets/posts/Frontend/JavaScript-Route/img2.gif)

`popstate` 이벤트는 history에서 상태가 pop 됬을 때 트리거되는데 이는 브라우저의 앞으로가기 / 뒤로가기에 해당된다.

즉시 실행 함수에서 window에 이벤트를 걸어보자.

```javascript
window.addEventListener("popstate", ({ state }) => {
  console.log(
    `location: ${document.location}, state: ${JSON.stringify(state)}`
  );
});
```

![popstate](/assets/posts/Frontend/JavaScript-Route/img3.png)

뒤로가기를 누르면 console이 출력되는걸 확인할 수 있다.

### popState로 Route 변경 시 렌더링 하기

여기서는 2가지 방법을 사용할 수 있다.

1. state에 데이터를 저장 후 바로 렌더링

2. state에 페이지 번호를 저장후 페이지 번호로 API 요청, 처리

정책에 따라 두 방법 중 적절하게 선택하면 될것 같다.

예를 들어, 캐싱해서 빠르게 데이터를 보여주고 싶다면 1번

항상 최신 데이터를 보여주고 싶다면 2번을 선택할 수 있다.

여기선 2번으로 구현했다.

pageManager에 원하는 페이지로 변경하는 함수를 추가해주자.

```javascript
const PageManager = () => {
  // 다른 함수들 생략

  const setPageNum = async (newPageNum) => {
    pageNum = newPageNum;
    const pages = await requestPage(pageNum);
    renderPosts(pages);
  };
  return { renderBeforePage, renderNextPage, setPageNum };
};
```

`history.pushState`와 이벤트 리스너 로직을 변경해주자.

```javascript
history.pushState({ pages }, pageNum, `?page=${pageNum}`);
```

```javascript
window.addEventListener("popstate", ({ state }) => {
  const pageNum = state === null ? 1 : state.page;
  pageManager.setPageNum(pageNum);
});
```

상태가 없을 경우 (주소가 `localhost:3000`)는 **state가 null**이므로 체크해줘야한다!

이제 뒤로가기를 하면 API가 호출되고 렌더링까지 되는걸 확인할 수 있다.

### 또 다른 문제점

![refresh-error](/assets/posts/Frontend/JavaScript-Route/img4.gif)

새로고침 시 즉시실행 함수가 실행되기 떄문에 첫 번째 페이지로 요청을 보내기 때문에 발생한다.

`document.location`에 uri 정보가 있으지 page 정보를 파싱해서 요청하도록 만들면 된다!
