---
layout: post
title: jest로 class 테스트하기
date: 2021-03-08 20:00:00 +0900
author: EarlyHail
categories: JavaScript
description: Jest를 이용하여 class 멤버함수 테스트하기
tags: [JavaScript, Jest, Test]
---

함수에서 클래스를 사용할 때, 클래스의 인스턴스를 주입받을 수도 있고 내부에서 직접 생성할 수 도 있다.

두 가지 방법을 각각 테스트해보자.

### 0. 테스트 타겟 코드

- **lecture.js**

  ```javascript
  class Lecture {
    constructor(lectureName) {
      this.lectureName = lectureName;
      this.students = [];
    }

    registerStudent(studentName) {
      this.students.push(studentName);
    }

    getStudents() {
      return this.students;
    }
  }

  module.exports = Lecture;
  ```

- **registerStudent.js**

  ```javascript
  const Lecture = require("./lecture");

  const injectInstanceRegisterStudent = (lecture, students) => {
    lecture.registerStudent(students);
  };

  const declareInstanceRegisterStudent = (lectureName, students) => {
    const lecture = new Lecture(lectureName);
    lecture.registerStudent(students);
  };

  module.exports = {
    injectInstanceRegisterStudent,
    declareInstanceRegisterStudent,
  };
  ```

### 1. 인스턴스 주입 테스트

```javascript
const { injectInstanceRegisterStudent } = require("../registerStudent");
const Lecture = require("../lecture");

jest.mock("../lecture");

describe("registerUser 테스트", () => {
  test("인스턴스 주입 호출 테스트", () => {
    const lectureName = "web";
    const lecture = new Lecture(lectureName);
    const studentName = "EarlyHail";

    injectInstanceRegisterStudent(lecture, studentName);

    expect(lecture.registerStudent).toBeCalledWith(studentName);
  });
});
```

`lecture.registerStudent`의 호출을 테스트하고 잘 통과한다.

### 2. 인스턴스 생성 테스트

```javascript
const { declareInstanceRegisterStudent } = require("../registerStudent");
const Lecture = require("../lecture");

jest.mock("../lecture");

describe("registerUser 테스트", () => {
  test("인스턴스 선언 호출 테스트", () => {
    const lectureName = "web";
    const studentName = "EarlyHail";

    declareInstanceRegisterStudent(lectureName, studentName);

    expect(Lecture).toBeCalledWith(lectureName);
    expect(Lecture.registerStudent).toBeCalledWith(studentName);
  });
});
```

![인스턴스 내부 선언 테스트](/assets/posts/JavaScript/class-testing/img1.png)

`lecture`의 멤버 함수가 mock 또는 spy된 함수가 아니라고 나온다.

이유는 정확히 모르겠으나 생성자와 this의 바인딩 순서와 연관이 있거나 객체가 생성될 때 jest에서 mocking을 시작하지 않을까 추측해본다.

어쨋든 테스트는 계속되어야 하니, 차선책을 사용해보자.

1.  jest.fn()과 jest.mock의 팩토리 함수 이용

    첫 번째 방법은 jest.mock의 두 번째 인자인 팩토리 함수에 객체함수를 구현하는 방법이다.

    ```javascript
    const { declareInstanceRegisterStudent } = require("../registerStudent");
    const Lecture = require("../lecture");

    const mockRegisterStudent = jest.fn((studentName) => {});

    jest.mock("../lecture", () => {
      return jest.fn().mockImplementation(() => {
        return { registerStudent: mockRegisterStudent };
      });
    });

    describe("registerUser 테스트", () => {
      afterEach(() => {
        jest.clearAllMocks();
      });
      test("인스턴스 선언 호출 테스트", () => {
        const lectureName = "web";
        const userName = "EarlyHail";

        declareInstanceRegisterStudent(lectureName, userName);

        expect(mockRegisterStudent).toBeCalledWith(userName);
      });
    });
    ```

2.  jest.fn()과 prototype을 이용

    두 번째 방법은 jest.fn()으로 mock 함수를 만들고 Class의 prototype에 등록하여 호출되는지 확인하는 방법이다.

    ```javascript
    const { declareInstanceRegisterStudent } = require("../registerStudent");
    const Lecture = require("../lecture");

    describe("registerUser 테스트", () => {
      test("인스턴스 선언 호출 테스트", () => {
        const lectureName = "web";
        const userName = "EarlyHail";

        const mockRegisterStudent = jest.fn((studentName) => {});
        Lecture.prototype.registerStudent = mockRegisterStudent;

        declareInstanceRegisterStudent(lectureName, userName);

        expect(mockRegisterStudent).toBeCalledWith(userName);
      });
    });
    ```

    mock 함수를 직접 prototype에 삽입해주기 떄문에 Lecture 파일 자체를 `jest.mock`을 이용하여 모킹할 필요가 없다.
