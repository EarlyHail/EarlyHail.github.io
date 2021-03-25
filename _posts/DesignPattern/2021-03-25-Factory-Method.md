---
layout: post
title: Factory Method 패턴
date: 2021-03-25 17:00:00 +0900
author: EarlyHail
categories: DesignPattern
description:
tags: [DesignPattern]
---

## Factory Method

객체를 생성하기 위해 인터페이스를 정의하지만, 어떤 클래스의 인스턴스를 생성할지에 대한 결정은 서브클래스가 내리도록 한다.

![factory-method-diagram1](/assets/posts/DesignPattern/Factory-Method/img1.png)

![factory-method-diagram2](/assets/posts/DesignPattern/Factory-Method/img2.png)

- Product

  팩토리 메소드가 생성하는 객체의 인터페이스가 정의된다.

- ConcreteProduct

  Product 클래스에 정의된 인터페이스를 구현한다.

- Creator

  Product 타입의 객체를 반환하는 팩토리 메소드를 선언한다. Creator는 팩토리 메소드를 기본적으로 구현하고 ConcreteProduct의 인스턴스를 반환한다.

  Product 객체를 생성하기 위해 Factory Method를 호출한다.

  Creator는 2가지 방식으로 구현된다.

  1. Creator가 추상클래스이고 팩토리 메소드의 구현을 제공하지 않는 경우

  2. Creator가 일반 클래스이고 팩토리 메소드에 대한 기본 구형을 제공하는 경우

- ConcreteCreator

  Factory Method를 재정의 하여 ConcreteProduct의 인스턴스를 반환한다.

### 1. 문제 상황

MazeGame의 `createMaze`는 미로를 생성하는 클래스이다.

```typescript
class MazeGame {
  createMaze() {
    const maze = new Maze();
    const room1 = new Room(1);
    const room2 = new Room(2);
    const Door = new Door(r1, r2);

    maze.addRoom(room1);
    maze.addRoom(room2);

    room1.setSide("East", new Wall());
    room1.setSide("West", door);

    room2.setSide("East", door);
    room2.setSide("West", new Wall());

    // 미로 생성 추가 작업...

    return maze;
  }
}
```

`MazeGame`은 maze 객체를 생성하기 위한 `createMaze` 인터페이스를 정의하고, 구현하였다.

미로의 종류는 여러개로 확장될수 있다. 예를 들어 방과 벽에 폭탄이 설치되어있는 컨셉인 BombMazeGame을 만들고 싶다고 해보자.

`new Room()`과 `new Wall()`을 `new BombRoom()`과 `new BombRoom()`으로 변경하는건 좋지 않다.

Abstract Factory처럼 각 미로마다 재료를 제공해주는 factory를 인자로 받는 방법은 이 상황에서 선택할만한 방법이다. 하지만 factory로 의존성을 제공받지 않고 MazeGame을 상속받아 필요한 부품만 교체하도록 해보자.

### 2. 팩토리 메소드 적용

```typescript
class MazeGame {
  makeMaze() {
    return new Maze();
  }
  makeWall() {
    return new Wall();
  }
  makeRoom(num: number) {
    return new Room(num);
  }
  makeDoor(room1: Room, room2: Room) {
    return new Door(room1, room2);
  }

  createMaze() {
    const maze = this.makeMaze();

    const room1 = this.makeRoom(1);
    const room2 = this.makeRoom(2);
    const door = this.makeDoor(room1, room2);

    maze.addRoom(room1);
    maze.addRoom(room2);

    room1.setSide("East", this.makeWall());
    room1.setSide("West", door);

    room2.setSide("East", door);
    room2.setSide("West", this.makeWall());

    // 미로 생성 추가 작업...

    return maze;
  }
}
```

`MazeGame`의 `make...`함수들은 모두 팩토리 메소드이다. 각 팩토리 메소드는 미로에 사용될 재료들을 반환한다.

위 코드만 보면 뭔가.... 디자인 패턴을 적용했다고 하기에는 애매한 느낌이 든다. 그냥 객체 생성을 메소드로 변경한것 뿐이라는 생각도 든다.

그럼 이제 Room과 Wall이 Bomb인 BombMazeGame을 만들어보자.

```typescript
class BombedMazeGame extends MazeGame {
  makeWall() {
    return new BombWall();
  }
  makeRoom(num: number) {
    return new BombRoom(num);
  }
}
```

이제 Factory Method를 사용하는 장점이 보인다. 재료를 변경할 때 상속을 이용하여 해당 재료를 생성해주는 Factory Method만 재정의해주면 된다.

Abstract Factory는 Product 생성에 필요한 여러 객체들을 묶어서 관리하고 Factory Method는 Product 생성에 필요한 객체들을 메소드로 추상화하여 관리한다.

Abstract Factory도 상속을 통하여 확장할 수 있고, Factory Method도 상속을 통하여 확장할 수 있으니 두 생성 패턴은 비슷한 목적을 다른 방식으로 풀어낸 패턴인것 같다.

### 언제 Factory Method를 적용해야 할 까

1. 클래스가 자신이 생성해야하는 객체의 클래스를 예측할 수 없을 때

   MazeGame의 createMaze에 필요한 재료가 미리 정해져있지 않을 때, 필요에 따라 MazeGame을 상속받아 구현하는 방식을 사용할 수 있다.

2. 생성할 객체를 기술하는 책임을 자신의 서브클래스가 지정했으면 할 때

   MazeGame의 createMaze를 추상 메소드로 선언만 해두고 BombMazeGame 또는 HiddenDoorMazeGame 서브클래스가 각자의 의도에 맞게 객체를 생성하도록 구현할 수 있다.
