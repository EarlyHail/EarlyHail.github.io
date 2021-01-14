---
layout: post
title: 다음 큰 숫자
date: 2021-01-14 20:00:00 +0900
author: EarlyHail
categories: "Algorithm"
description: 프로그래머스 연습문제 Level2 다음 큰 숫자
tags: "Algorithm"
---

## 문제 이름

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/12911#)

### JavaScript

```javascript
function solution(n) {
  const binary = "0" + n.toString(2);
  if (binary.includes("0")) {
    const binaryAddZero = binary;
    const lastOfOne = binaryAddZero.lastIndexOf("1");
    const lastOfZero = binaryAddZero.lastIndexOf("0");
    const beforeLastOfOne = binaryAddZero.substring(0, lastOfOne);
    const lastOfZeroOfbeforeLastOfOne = beforeLastOfOne.lastIndexOf("0");
    const countOfOneOfLastOfZeroBeforeLastOfOne =
      [...binaryAddZero].filter((bin, index) => {
        if (index > lastOfZeroOfbeforeLastOfOne && bin == "1") return bin;
      }).length - 1;
    const result = (
      binaryAddZero.substring(0, lastOfZeroOfbeforeLastOfOne) + "1"
    )
      .padEnd(binaryAddZero.length - countOfOneOfLastOfZeroBeforeLastOfOne, "0")
      .padEnd(binaryAddZero.length, "1");
    return parseInt(result, 2);
  } else {
    const result = binary.replace("1", "10");
    return parseInt(result, 2);
  }
}
```

### 접근방법

- 조건에 맞는 가장 큰 숫자를 만드는 경우는 2가지로 나뉜다.

  1.  2진법으로 변환된 숫자에 0이 없는 경우

  2.  2진법으로 변환된 숫자에 0이 있는 경우

- 2진법으로 변환된 숫자에 0이 없는 경우

  예를 들어, `1111`인 경우, 만들 수 있는 다음 큰 숫자는 `10111`이다.

  제일 앞에 있는 `1`을 `10`으로 바꿔주면 된다.

- 2진법으로 변환된 숫자에 0이 있는 경우

  1. 마지막 1을 찾는다.

  2. 마지막 1앞에 있는 마지막 0을 찾는다.

  3. 마지막 1앞에 있는 마지막 0을 1로 바꾼다.

  4. 1로 바꾼 0의 뒤에 있는 모든 1을 뒤로밀착 시킨다.

  예를 들어, `1001110`은 `1011100`으로 바뀐 뒤 바뀐 숫자 이후의 1이 뒤로 밀착되어 `1010011`로 바뀐다.

  하지만 문제가 생기는데 마지막 1 앞에 0이 없는 경우이다.

  `1110`의 경우 마지막 1 앞에있는 마지막 0을 찾을 수 없다.

  이 예외 케이스는 간단하게 해결할 수 있는데, 맨 앞에 0을 붙여주는 것이다.

  `01110`은 `11100`으로 바뀐 뒤 `10011`로 바뀌어 문제의 정답이 된다.
