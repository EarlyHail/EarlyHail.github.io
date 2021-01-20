---
layout: post
title: Jest와 MSW를 이용한 API Test
date: 2021-01-19 20:00:00 +0900
author: EarlyHail
categories: Frontend
description: Jest와 MSW를 이용하여 API 테스트 하기
tags: [Frontend, Test]
---

## MSW

MSW(Mock Server Worker)는 간단하게 말하면 테스트용 서버를 만드는 것이다.

테스트 서버를 따로 만들어 정해진 응답을 리턴하도록 만들어 Backend와 독립된 Frontend 테스트를 가능하게 해준다.

## 1. UserList 컴포넌트 만들기

- UserListContainer.js

  ```javascript
  import React, { useState } from "react";
  import UserListPresenter from "./UserListPresenter";

  const UserListContainer = () => {
    const [users, setUsers] = useState([]);
    const requestUsers = async () => {
      const response = await fetch("http://111.111.111.111:3000/users", {
        method: "GET",
      });
      const newUsers = await response.json();
      if (newUsers.length > 0) {
        setUsers(() => newUsers);
      }
    };
    return <UserListPresenter users={users} requestUsers={requestUsers} />;
  };
  export default UserListContainer;
  ```

- UserListPresenter.js

  ```javascript
  import React from "react";
  import styled from "styled-components";

  const UserList = styled.div`
    display: flex;
    flex-direction: column;
    margin: 20px;
    font-size: 20px;
    font-weight: bold;
  `;

  const UserListPresenter = ({ users, requestUsers }) => {
    return (
      <>
        <UserList>
          {users.map((user) => {
            return (
              <div key={user.id}>
                {user.id} : {user.name}
              </div>
            );
          })}
        </UserList>
        <button type="button" onClick={requestUsers}>
          유저 새로고침
        </button>
      </>
    );
  };
  export default UserListPresenter;
  ```

`UserList`는 사용자 리스트를 보여주고 버튼을 누르면 서버로부터 UserList를 받아 사용자 리스트를 새로고침 하는 컴포넌트이다.

그럼 서버를 만들고 서버에 직접 요청해서 테스트를 하면 될까???

## 2. API 테스트의 문제점

1. 테스트는 의존성을 낮춰야 한다.

   `사용자 리스트`는 일반적으로 데이터베이스에 저장되어 있을거다.

   예상하는 사용자가 있을 수도 있고, 없을수도 있기 때문에 테스트 하기 전에 데이터를 DB에 미리 저장을 해 줘야 한다.

   그런데 테스트가 DB에 의존하는게 맞는가?

   실수로 필요한 정보를 지워버리면 문제가 생길 수도 있다.

   따라서 API 테스트는 Database에 의존하면 안된다.

2. 데이터 CRUD는 Backend 영역이다.

   물론 Frontend에서 DB접근을 할 수 있는 좋은 방법이나 기술이 많지만 지금 테스트 하고 있는것은 서버에서 정보를 받을 때 이다.

   데이터를 쓰고, 읽고, 수정하고, 삭제하는 것은 백엔드가 할 일이고 프론트엔드 테스트는 신경을 최대한 쓰지 않아야 한다.

   그냥 **데이터가 정상적으로 온다는 조건 하에 잘 렌더링 하면된다.**

## API 테스트 방법

1. fetch 함수를 mock 함수로 대체

   `UserList`의 경우 `UserListPresenter`를 테스트하고 `requestUsers`를 mock함수로 대체하는 것이다.

   하지만 상태관리 등 UserListContainer의 역할을 테스트 코드 내에 구현해주어야 한다.

2. 테스트를 위한 API 서버 구현

   `테스트만을 위한` 간단한 API 서버를 만드는 것이다.

   API Request가 발생하도록 놔두고 요청을 캡쳐 하여 테스트 환경에서 예상 하능한 값을 반환하도록 만든다.

## 3. Jest와 MSW로 API 테스트하기

### 1) 테스트코드 작성

### 2) MSW 설치하기

```
npm i -D msw
```

### 2) MSW 서버 만들기

`src/__mock__`폴더를 생성한다.

- Handler 생성

  Handler는 Controller의 역할을 한다. (express의 router)

  `__mock__/handlers.js`에 핸들러를 생성한다.

  ```javascript
  import { rest } from "msw";
  export const handlers = [
    rest.get("http://111.111.111.111:3000/users", (req, res, ctx) => {
      return res(
        ctx.status(200),
        ctx.json([
          {
            id: "EarlyHail",
            name: "Hojin Lee",
          },
          {
            id: "user1",
            name: "Other User",
          },
        ])
      );
    }),
  ];
  ```

- Server 생성

  Server는 Controller를 모아주는 역할을 한다. (express의 app)

  ```javascript
  import { setupServer } from "msw/node";
  import { handlers } from "./handlers";
  export const server = setupServer(...handlers);
  ```

- test 환경설정

  이제 테스트가 실행될 때 서버가 실행되도록 만들어보자.

  `Jest`의 `beforeAll`, `afterEach`, `afterAll`을 이용해서 서버를 시작하고, 초기화하고, 종료해준다.

  ```javascript
  import { server } from "./__mocks__/server.js";
  beforeAll(() => server.listen());
  afterEach(() => server.resetHandlers());
  afterAll(() => server.close());
  ```

### 3) 테스트코드 작성

`__test__/UserList.test.js`에 테스트 코드를 작성해보자.

```javascript
import React from "react";
import { screen, fireEvent, render, waitFor } from "@testing-library/react";
import UserListContainer from "../UserListContainer";

describe("UserList Test", () => {
  beforeEach(() => {
    render(<UserListContainer />);
  });
  test("유저 새로고침 버튼 클릭 시 유저를 보여줍니다.", async () => {
    fireEvent.click(screen.getByText("유저 새로고침"));
    await waitFor(() => screen.getByText(/EarlyHail/g));
  });
});
```

### 결과

![TestResult](/assets/posts/Frontend/Jest-API-Test-MSW/test1.png)

버튼 클릭 이벤트만 활성화 시켜줘도 테스트가 잘 된다!

거의 API 서버를 만들어 주는 방식이긴 하지만 테스트 코드 자체가 깔끔해지기 때문에 좋은 방식인것 같다.
