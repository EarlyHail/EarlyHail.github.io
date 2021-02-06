---
layout: post
title: Garbage Collection
date: 2021-02-06 22:00:00 +0900
author: EarlyHail
categories: JavaScript
description: Gargabe Collection의 개념과 알고리즘에 대해 알아보자
tags: [JavaScript, GarbageCollection]
---

### Garbage

**Garbage**는 Program이 접근하지 못하는 Heap 메모리 block이다.

**Garbage**는 Heap에 할당된 블록에 대한 **참조가 존재하지 않거나** / **더 이상 존재하지 않는 블록에 대한 참조**가 있을 때 발생한다.

## Garbage Collection

**Garbage Collection**은 사용되지 않는 Heap 메모리의 영역들을 회수하는 것이다.

### 1. Reference Counting

- 자신을 참조하고 있는 객체의 개수를 계산하여 더 이상 필요하지 않은 객체의 할당을 해제한다.

- Heap에 `할당이 발생할 때 마다` 알고리즘이 실행된다.

- Garbage 발생 즉시 할당을 해제할 수 있다.

- 순환 참조일 경우 Garbage를 수거할 수 없는 상황이 발생한다.

  ![circular-reference](/assets/posts/Etc/Garbage-Collection/img1.png)

### 2. Mark-and-Sweep

- 접근 가능한 객체들을 표시하여 표시되지 않은 객체의 할당을 해제한다.

- Heap Overflow가 발생할 때 알고리즘이 실행된다.

- 대부분의 최신 브라우저의 JavaScript는 Mark-and=Sweep 알고리즘을 사용한다.

### Copy Collection

- Heap을 두 영역으로 나눠 한 영역만 사용한다.

- 영역의 접근 가능한 객체들을 다른 영역으로 copy하고 원래 사용하던 영역을 삭제한다.
