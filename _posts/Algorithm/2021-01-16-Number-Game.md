---
layout: post
title: 숫자 게임
date: 2021-01-16 22:30:00 +0900
author: EarlyHail
categories: "Algorithm"
description: 프로그래머스 연습문제 Level3 숫자 게임 JavaScript
tags: "Algorithm"
---

## 숫자 게임

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/12987)

### JavaScript

## 풀이1

```javascript
function solution(A, B) {
  let winCount = 0;
  A.sort((prev, next) => prev - next);
  B.sort((prev, next) => prev - next);
  for (let i = 0; i < A.length; i++) {
    const targetNum = A[i];
    const index = findBiggerIndex(B, targetNum);
    if (index === false) {
      break;
    } else {
      B[index] = -1;
      winCount++;
    }
  }
  return winCount;
}
const findBiggerIndex = (numArr, target) => {
  for (let i = 0; i < numArr.length; i++) {
    if (numArr[i] != -1 && numArr[i] > target) {
      return i;
    }
  }
  return false;
};
```

- 소요시간 : 20분

### 접근방법

1. A와 B를 오름차순으로 정렬한다.

2. 각 A에 대해 A보다 큰 수중 가장 작은 B를 찾는다.

   - 찾으면 count를 더해주고, -1을 넣어 사용한 수라는 것을 체크해준다.

   - 찾지 못하면 해당 A 보다 더 큰 수에 대해서도 당연히 찾지 못할 것이므로 더이상 검색을 하지 않는다.

- 문제의 정답은 다 맞았지만 효율성 테스트를 전부 통과하지 못했다.

- `findBiggerIndex`가 숫자와 false를 넘겨줄 수 있는건 분명한 JavaScript의 장점이다.

  하지만 false를 넘겼을 때 조건체크를 하려면 꼭 `===`를 사용해야 한다!

### 풀이2

위 풀이에서, A보다 더 큰 수 중 가장 작은 B를 찾았을 때, 다음 A에 대해 해당 B 이후만 검색하면 된다.

```javascript
function solution(A, B) {
  let winCount = 0;
  A.sort((prev, next) => prev - next);
  B.sort((prev, next) => prev - next);
  let indexA = 0;
  let indexB = 0;
  for (let i = 0; i < A.length; i++) {
    if (A[indexA] < B[indexB]) {
      winCount++;
      indexA++;
      indexB++;
    } else {
      indexB++;
    }
  }
  return winCount;
}
```

### 접근방법

1. A와 B의 검색용 인덱스 `indexA`, `indexB`를 정의한다.

2. A[indexA]와 B[indexB]를 비교한다.

   - A[indexA]보다 B[indexB]가 더 클 경우, 이길 수 있는 경우이다.

     count를 더해주고 indexA와 indexB를 각각 1씩 더해준다.

   - A[indexA]보다 B[indexB]가 더 작거나 같을 경우, 이길 수 없는 경우이다.

     다음 이길 수 있는 B를 찾기 위해 indexB만 1을 더해준다.
