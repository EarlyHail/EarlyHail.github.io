---
layout: post
title: Transaction - ACID 원칙
date: 2021-01-19 13:00:00 +0900
author: EarlyHail
categories: Database
description:
tags: Database
---

## 1. 트랜잭션

정의 : 하나의 논리적 작업 단위를 구성하는 일련의 연산들의 집합

예를 들어, 친구의 계좌에 돈을 입금할 때, 친구가 자신의 계좌에서 돈을 인출하는 상황을 가정해보자.

나와 친구 계좌에는 각 100만원씩 있고, 나는 50만원을 보내고 친구는 50만원을 인출하려고 한다.

### 정상적인 예시

- 내가 돈을 입금할 때

  1. 내 계좌에서 돈을 빼서 저장한다. (나:50 친구:100)

  2. 친구 계좌에 돈을 추가해 저장한다. (나:50 친구:150)

- 친구가 돈을 인출할 때

  3. 친구의 계좌에서 돈을 빼서 저장한다. (나:50 친구:100 현금:50)

### 비정상적인 예시

2와 3의 순서가 바뀐다면?

1. 내 계좌에서 돈을 빼서 저장한다. (나:50 친구:100)

2. 친구의 계좌에서 돈을 빼서 저장한다. (나:50 친구:50 현금:50)

3. 친구 계좌에 돈을 추가해 저장한다. (나:50 친구:150 현금:50)

돈이 복사가 된다!!!

이러한 상황을 방지해주는것이 트랜잭션 (transaction) 이다.

## 2. 트랜잭션의 특성

트랜잭션의 특징은 `ACID라고도 불린다.

### 원자성 (Atomicity)

- 트랜잭션을 구성하는 연산들이 모두 정상적으로 실행되거나, 하나도 실행되지 않아야 한다. (All or Nothing)

- 예를 들어, 돈을 송금할 때 내 계좌에서 돈이 빠져나간 뒤 문제가 생겨 타겟 계좌에 돈을 더하지 못했을 경우, `돈이 빠져나가기 전의 상태로 되돌아가야`한다.

### 일관성 (Consistency)

- 트랜잭션이 성공적으로 수행된 뒤 데이터베이스는 일관성 있는 상태여야 한다.

- 예를 들어, 트랜잭션에서 든 예시 처럼 송금하기 전의 돈의 합과 송금을 완료했을 때의 돈의 합이 같아야 한다.

### 고립성 (Isolation)

- 한 트랜잭션이 실행되는 동안 다른 트랜잭션이 접근할 수 없다.

  위의 비정상적 예시에서, 송금 트랜잭션이 동작하는 동안 인출 트랜잭션이 끼어들었다. 이는 고립성을 위반한 것이며 이 결과로 일관성이 깨진 경우이다.

### 영속성 (Durability)

- 성공적으로 수행된 트랜잭션은 영원히 반영이 되어야 한다.
