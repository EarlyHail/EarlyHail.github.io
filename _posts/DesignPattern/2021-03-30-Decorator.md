---
layout: post
title: Decorator 패턴
date: 2021-03-30 16:00:00 +0900
author: EarlyHail
categories: DesignPattern
description:
tags: [DesignPattern]
---

## Decorator

객체에 동적으로 새로운 책임을 추가할 수 있게 한다.

기능을 추가할 때 서브클래스를 생성하는 것보다 유연한 방법을 제공한다.

![decorator-diagram](/assets/posts/DesignPattern/Decorator/img1.png)

- Component

  동적으로 추가할 서비스를 가질 가능성이 있는 객체들에 대한 인터페이스

- ConcreteComponent

  추가적인 서비스가 실제로 정의되어야 할 필요가 있는 객체

- Decorator

  Component 객체에 대한 참조자를 관리하면서 Component에 정의된 인터페이스를 만족하도록 인터페이스를 정의

- ConcreteDecorator

  Component에 새롭게 추가할 서비스를 실제로 구현하는 클래스

### Decorator 패턴 적용

- Component

  ```typescript
  interface VisualComponent {
    draw(): void;
    resize(): void;
  }
  ```

- ConcreteComponent

  ```typescript
  class TextView implements VisualComponent {
    draw() {
      // draw text view
      console.log("draw text view");
    }
    resize() {
      // resize text view
      console.log("resize text view");
    }
  }
  ```

- Decorator

  ```typescript
  class Decorator implements VisualComponent {
    constructor(private component: VisualComponent) {}
    draw() {
      this.component.draw();
    }
    resize() {
      this.component.resize();
    }
  }
  ```

- ConcreteDecorator

  ```typescript
  class BorderDecorator extends Decorator {
    constructor(visualComponent: VisualComponent, private borderWidth: number) {
      super(visualComponent);
    }
    private drawBorder(border: number) {
      // draw border
      console.log("draw Border");
    }
    draw() {
      Decorator.prototype.draw.call(this);
      this.drawBorder(this.borderWidth);
    }
  }

  class ScrollDecorator extends Decorator {
    constructor(
      visualComponent: VisualComponent,
      private scrollHeight: number
    ) {
      super(visualComponent);
    }
    private drawScroll(border: number) {
      // draw border
      console.log("draw Scroll");
    }
    draw() {
      Decorator.prototype.draw.call(this);
      this.drawScroll(this.scrollHeight);
    }
  }
  ```

`TextView`는 일반적인 텍스트를 보여주는 화면을 출력하는 기능을 제공한다.

여기에 경우에 따라 Scroll과 Border를 사용하여 TextView를 꾸밀 것이다.

`VirtualComponent`(Component) 인터페이스에 따라 `TextView`(Concrete Component)와 `Decorator`를 구현한다.

`Decorator`는 VisualComponent를 인자로 받아 동일한 이름의 함수를 실행시킨다. 이 때 Decorator는 자신이 **어떤 기능을 실행시키는지 모른다.** (Decorator의 서브클래스일 수도 있고, ConcreteComponent일 수도 있다.)

`Decorator`를 상속받으며 `VirtualComponent`인터페이스에 따라 각 Decorator의 용도에 맞는 기능을 구현한 뒤 Decorator(부모 클래스)의 동일한 기능을 호출하면 또 다른 VirtualComponent의 구현체의 기능을 호출한다.

이를 통해 ConcreteComponent와 Decorator의 서브클래스들이 모두 동작하게 된다.

코드를 보면 더 쉽게 이해될것 같다.

- 사용

  ```typescript
  class Windows {
    private contents: VisualComponent;
    setContents(contents: VisualComponent) {
      this.contents = contents;
    }
    drawContents() {
      this.contents.draw();
    }
    resizeContents() {
      this.contents.resize();
    }
  }

  const windows = new Windows();
  windows.setContents(new TextView());
  windows.drawContents();

  windows.setContents(new BorderDecorator(new TextView(), 100));
  windows.drawContents();
  console.log("=======================");

  windows.setContents(
    new ScrollDecorator(new BorderDecorator(new TextView(), 100), 300)
  );
  windows.drawContents();
  console.log("=======================");
  ```

  ![decorator-result](/assets/posts/DesignPattern/Decorator/img2.png)

### 장점

- 단순한 상속보다 유연한 확장이 가능하다.

- 클래스 계통의 상부에 많은 기능이 누적되는 상황을 피할 수 있다.

  기능을 단순한 구성 요소들의 조합으로 구성할 수 있다.

### 단점

- 객체 수가 증가한다.

  작은 규모의 객체가 많이 생기기 때문에 모두 이해하고 수정하는데에 코드에 대한 이해도와 시간이 소요될 수 있다.
