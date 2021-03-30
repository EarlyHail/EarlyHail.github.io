---
layout: post
title: Template Method 패턴
date: 2021-03-30 18:00:00 +0900
author: EarlyHail
categories: DesignPattern
description:
tags: [DesignPattern]
---

## Template Method

객체의 연산에는 알고리즘의 뼈대만을 정의하고 각 단계에서 수행할 구체적 처리는 서브클래스로 미룬다.

알고리즘의 구조 자체는 그대로 놔둔 채 알고리즘의 각 단계 처리를 서브클래스에서 재정의할 수 있게 한다.

![template-method--diagram](/assets/posts/DesignPattern/Template-Method/img1.png)

- AbstractClass

  서브클래스들이 재정의를 통해 구현해야 하는 알고리즘 처리 단계 내의 기본 연산을 정의

  알고리즘의 뼈대를 정의하는 템플릿 메소드 구현

  템플릿 메소드는 AbstractClass에 정의된 연산 또는 다른 객체의 연산, 기본 연산도 호출한다.

- ConcreteClass

  서브클래스마다 달라진 알고리즘 처리 단계를 수행하기 위한 기본 연산을 구현

### Template Method 패턴 적용

- AbstractClass

  ```typescript
  abstract class View {
    abstract doDisplay(): void;
    setFocus() {
      // View를 보여주기 전 Focus를 설정하는 로직
    }
    resetFocus() {
      // View를 보여준 이후 Focus를 해제하는 로직
    }
    display() {
      this.setFocus();
      this.doDisplay();
      this.resetFocus();
    }
  }
  ```

- ConcreteClass

  ```typescript
  class ViewGraph extends View {
    doDisplay() {
      // 그래프를 보여주는 로직
    }
  }

  class ViewVideo extends View {
    doDisplay() {
      // 영상을 보여주는 로직
    }
  }
  ```

Class Diagram 만큼 코드도 매우 간단하다.

`View`(Abstract Class)에는 View 이전에 할일, 이후에 할일에 대한 뼈대 로직만 작성하 **무엇을 어떻게 보여줄지**에 대한 구체적인 처리는 서브클래스로 미룬다.

`ViewGraph`와 `ViewVideo`는 View의 서브클래스로 View에 대한 구체적인 처리를 구현한다.

Template Method 패턴은 단순해 보이지만 꽤 사용될 곳이 많을것 같다.

예를들어 Frontend에서 API 요청을 보낼 때 필요한 Global 정보를 조회하는 로직, API 요청 이후 처리해야할 공통 로직을 AbstractClass에 작성한다.

그 이후 서브클래스에서 각 API요청, API 요청 이후 개별 처리에 대한 세부 로직을 ConcreteClass에 작성하는 방식을 적용할 수도 있을것 같다.
