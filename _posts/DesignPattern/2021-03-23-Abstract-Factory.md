---
layout: post
title: 추상 팩토리
date: 2021-03-23 22:00:00 +0900
author: EarlyHail
categories: DesignPattern
description:
tags: DesignPattern
---

## 추상 팩토리

상세화된 서브클래스를 정의하지 않고도 서로 관련있거나 독립적인 여러 객체들을 묶어주기 위한 인터페이스 제공

### 1. 문제 상황

MazeGame의 `createMaze`는 미로를 생성하는 클래스이다.

```typescript
class MazeGame {
  static createMaze() {
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

`createMaze`는 미로를 생성하고, 1번 2번 방을 생성하고, 문을 생성하여 1번방과 2번방을 문으로 연결한다.

미로의 종류는 여러개가 있을수 있으며 각 미로에 방, 문, 벽은 다를 수 있으며 또 재사용될 수 있다.

만약 일반 미로가 아닌 숨겨져 있는 컨셉의 HiddenMaze를 만들고 싶으면 어떻게해야할까? 각 재료의 생성 코드를 `const Door = new HiddenDoor(r1, r2);`처럼 변경하는건 당연히 답이 될 수 없다.

`어떤 재료들을 가지고 미로를 만들지 결정하는 부분을 분리` 한다면 각 미로에 대해 원하는 재료를 조합하여 미로를 생성할 수 있다.

### 2. 추상 팩토리 적용

각 미로에 필요한 재료들을 묶은 `MazeFactory`를 인자로 받아 인자를 통해 재료를 제공받도록 변경해보자.

```typescript
interface MazeFactory {
  makeMaze: () => Maze;
  makeWall: () => Wall;
  makeRoom: (num: number) => Room;
  makeDoor: (room1: Room, room2: Room) => Door;
}

class MazeGame {
  static createMaze(factory: MazeFactory) {
    const maze = factory.makeMaze();
    const room1 = factory.makeRoom(1);
    const room2 = factory.makeRoom(2);
    const door = factory.makeDoor(room1, room2);

    maze.addRoom(room1);
    maze.addRoom(room2);

    room1.setSide("East", factory.makeWall());
    room1.setSide("West", door);

    room2.setSide("East", door);
    room2.setSide("West", factory.makeWall());

    // 미로 생성 추가 작업...

    return maze;
  }
}
```

`createMaze`가 `MazeFactory`의 인스턴스를 인자로 받아 maze, room, door, wall을 생성해주는 함수를 사용하여 미로를 생성하도록 하였다.

이제 우리는 MazeFactory 인터페이스를 구현하여 미로에 필요한 재료가 Door든, HiddenDoor든, TrapDoor든 재료들을 잘 조합하여 팩토리 메소드를 생성하고 이를 `MazeGame`에게 제공해주면 쉽게 미로를 생성할 수 있다.

```typescript
const normalMazeFactory = new NormalMazeFactory();
const normalMaze = MazeGame.createMaze(normalMazeFactory);

const hiddenMazeFactory = new HiddenDoorMazeFactory();
const hiddenMaze = MazeGame.createMaze(hiddenMazeFactory);
```

### 장점

- 구체적인 클래스를 분리한다.

  지금은 간단하게 미로의 재료를 생성만 하지만 인스턴스를 생성한 뒤 설정이나 생성 과정에서 추가 작업이 필요하다면 이를 팩토리 클래스의 로직으로 분리할 수 있다.

  따라서 구체적인 구현 클래스가 사용자 (`createMaze`)로부터 분리된다.

- 생성에 필요한 재료 클래스들을 쉽게 대체할 수 있다.

  MazeFactory는 하나의 미로를 만드는데 필요한 모든 객체들을 제공하기 때문에 한번에 변경이 가능하다. 따라서 팩토리를 변경하는건 어렵지 않다.

- 일관성을 보장한다.

  room, door, wall이 하나의 미로에만 맞게 설게되었을 경우에 추상 팩토리를 사용하면 오직 하나의 팩토리를 통해 미로의 재료를 제공하므로 일관성을 보장할 수 있다.

### 단점

- 새로운 종류의 제품을 제공하기 어렵다.

  Factory의 재료를 일부 변경한다면 추상 팩토리는 변경에 유연할 수 있지만 새로운 제품을 만들기 위해 기존의 추상 팩토리를 확장하는것은 쉽지 않다.

  팩토리는 하나의 목적에 맞게 설계되어있기 때문이다. 따라서 팩토리의 구현의 많은 부분을 변경해야 한다.

  새로운 기능이 추가되거나, 새로운 재료가 추가된다면 인터페이스를 변경하고, 모든 서브클래스에 구현을해주어야한다.
