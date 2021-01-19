---
layout: post
title: Jest를 이용한 React Test
date: 2021-01-19 16:00:00 +0900
author: EarlyHail
categories: Frontend
description: Jest를 이용하여 React Component 테스트 하기
tags: Frontend
---

## 1. 테스트 환경 설정하기

### CRA

Create React App를 사용해서 React 개발 환경을 만들어보자

```shell
npx create-react-app react-test
cd react-test
```

### Styled Component

간단한 styling을 위해 `Styled Components`를 사용한다.

```shell
npm i styeld-components
```

### Jest

JavaScript 라이브러리는 `jest`를 사용한다.

`jest`는 CRA에 포함되어 있으므로 설치하지 않아도 된다.

### React Testing Library

React Component 테스트를 위해 React Testing Library를 사용한다.

```shell
npm install --save-dev @testing-library/react
```

## 2. Counter 구현

간단한 counter를 구현해보자.

<details>
  <summary>CounterContainer.js</summary>

```javascript
import React, { useState } from "react";
import CounterPresenter from "./CounterPresenter";

const CounterContainer = () => {
  const [counter, setCounter] = useState(0);
  const increment = () => {
    setCounter(() => counter + 1);
  };
  const decrement = () => {
    setCounter(() => counter - 1);
  };
  return (
    <CounterPresenter
      counter={counter}
      increment={increment}
      decrement={decrement}
    />
  );
};
export default CounterContainer;
```

</details>

<details>
  <summary>CounterPresenter.js</summary>

```javascript
import React from "react";
import styled from "styled-components";

const CounterDiv = styled.div`
  display: flex;
  justify-content: center;
  margin-top: 30px;
  font-size: 20px;
  font-weight: bold;
`;
const CounterPresenter = ({ counter, increment, decrement }) => {
  return (
    <CounterDiv>
      <button type="button" onClick={decrement}>
        -
      </button>
      <div id="counter">{counter}</div>
      <button type="button" onClick={increment}>
        +
      </button>
    </CounterDiv>
  );
};
export default CounterPresenter;
```

</details>

<details>
  <summary>App.js</summary>

```javascript
import CounterContainer from "./CounterContainer";

function App() {
  return <CounterContainer />;
}

export default App;
```

</details>

## 3. 테스트 해보기

`src/__test__` 폴더를 만들어서 테스트 코드를 만들어보자.

<details>
  <summary>__test__/Counter.test.js</summary>

```javascript
import React from "react";
import { screen, fireEvent, render } from "@testing-library/react";
import CounterContainer from "../CounterContainer";

describe("Counter Test", () => {
  test("첫 번째 카운터는 0입니다.", () => {
    render(<CounterContainer />);
    const counter = document.querySelector("#counter");
    expect(counter.innerHTML).toBe("0");
  });
  test("+ 클릭 시 카운터는 1입니다.", () => {
    render(<CounterContainer />);
    fireEvent.click(screen.getByText("+"));
    const counter = document.querySelector("#counter");
    expect(counter.innerHTML).toBe("1");
  });
  test("- 클릭 시 카운터는 0입니다.", () => {
    render(<CounterContainer />);
    fireEvent.click(screen.getByText("-"));
    const counter = document.querySelector("#counter");
    expect(counter.innerHTML).toBe("-1");
  });
});
```

</details>

`npm test`로 테스트를 실행시켜보자.

![testresult](/assets/posts/Frontend/Jest-React-Test/test1.png)

### 테스트 코드

`test()`는 하나의 테스트 케이스를 작성할 때 사용한다.
`describe()`는 여러 테스트케이스를 하나로 묶을 때 사용한다.

- render()로 컴포넌트를 렌더링하면 document나 screen을 이용하여 Element를 접근할 수 있다.

  - `document`는 일반적인 브라우저 객체처럼 querySelector, getElementByClassName 같은 함수를 사용할 수 있다.

  - screen은 `testing-library-react`의 다양한 함수를 이용하여 Element를 접근할 수 있다.

1. 각 테스트에서는 `CounterContainer`를 렌더링 한다.

2. `screen.getByTest`로 버튼을 찾는다.

3. `fireEvent.click`으로 버튼에 클릭 이벤트를 발생시킨다.

4. 클릭의 결과로 `#counter`가 변경됬는지 확인한다.

### 왜 render()를 매번 써야하는가

- `testing-library-react`는 각 test가 끝나면 `cleanup`함수를 실행시킨다.

  따라서 하나의 test가 종료되면 `CounterContainer`는 사라지기 때문에 다시 렌더링 해줘야 한다.

  cleanup을 막을 순 있지만 지양해야 하고 render 중복 코드를 제거해보자.

### BeforeEach

<details>
  <summary>__test__/Counter.test.js</summary>

```javascript
describe("Counter Test", () => {
  beforeEach(() => {
    render(<CounterContainer />);
  });
  test("첫 번째 카운터는 0입니다.", () => {
    const counter = document.querySelector("#counter");
    expect(counter.innerHTML).toBe("0");
  });
  test("+ 클릭 시 카운터는 1입니다.", () => {
    fireEvent.click(screen.getByText("+"));
    const counter = document.querySelector("#counter");
    expect(counter.innerHTML).toBe("1");
  });
  test("- 클릭 시 카운터는 0입니다.", () => {
    fireEvent.click(screen.getByText("-"));
    const counter = document.querySelector("#counter");
    expect(counter.innerHTML).toBe("-1");
  });
});
```

</details>

위 코드처럼 `beforeEach`에 callback함수를 등록하고 그 안에 test 전에 실행될 기능들을 추가하면 각 테스트 시작 전에 실행시킬 수 있다!

이를 이용해서 render나 테스트 전 변수 설정, 환경설정 등을 할 수도 있다.

beforeEach말고 afterEach도 있다!
