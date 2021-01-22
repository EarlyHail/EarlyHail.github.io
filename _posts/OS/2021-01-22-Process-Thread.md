---
layout: post
title: 프로세스와 스레드
date: 2021-01-22 22:00:00 +0900
author: EarlyHail
categories: OS
description: 프로세스, 스레드, 멀티 프로세스, 멀티 스레드 정리하기
tags: OS
---

### 프로그램

파일시스템에 존재하는 실행 파일( `.exe` )

## 프로세스

### 프로세스의 정의

1. 실행중인 프로그램

2. 메모리에 올라와 cpu를 할당받아 실행되고 있는 상태

3. 스택(복귀주소, 로컬변수 등의 임시적 자료) + 데이터 섹션(전역변수) + 힙(동적 할당을 위한 공간) 으로 이루어짐

### 프로세스의 상태

![Thread](/assets/posts/OS/Process-Thread/img1.png)

출처 : [https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/3_Processes.html
](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/3_Processes.html)

new : 새로운 프로세스 생성

ready : 실행 대기 (자원 할당 대기)

running : 명령어들이 실행되고 있음

waiting : 다른 작업을 기다리는 상태 (ex. IO)

terminated : 프로세스의 실행이 종료된 상태

## 스레드

- CPU 이용의 기본 단위

- ID, PC, 레지스터, 스택으로 구성됨

- 같은 프로세스에 속한 스레드들과 자원을 공유함

- 싱글 스레드 vs 멀티 스레드

  ![Thread](/assets/posts/OS/Process-Thread/img2.png)

  출처 : [https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/4_Threads.html](https://www.cs.uic.edu/~jbell/CourseNotes/OperatingSystems/4_Threads.html)

- 스레드의 장점

  1. 응답성

     CPU Bound process가 진행중일 때 별도의 스레드에서 진행중이라면 바로 응답할 수 있음

  2. 자원 공유

     스레드는 자신이 속한 프로세스의 자원을 공유함

     프로세스간 자원 공유는 Messaage Passing, Pipeline등을 사용해야해서 복잡함

  3. 경제성

     스레드를 생성하는게 프로세스를 생성하는것보다 훨씬 비용이 작다.

  4. 규모 적응성 (Scalability)

     다중 프로세서 구조일 때 각각의 스레드가 다른 프로세서에서 진행될 수 있음

## 멀티 프로세스 vs 멀티 스레드

멀티 프로세스는 말 그대로 여러개의 프로그램이 한번에 동작하는 것이다.

멀티 프로세스 + 싱글 스레드 환경에서는 각 프로세스를 돌아가며(Context Switching) 실행하며 한번에 돌아가는 것처럼 동작한다.

멀티 프로세스 + 멀티 스레드 환경에서는 각 프로세스가 동시(simultaneous)에 동작한다.

---

모든 이미지의 원 출처는 [Abraham Silberschatz, Greg Gagne, and Peter Baer Galvin, "Operating System Concepts, Ninth Edition"](https://www.os-book.com/OS9/) 입니다.
