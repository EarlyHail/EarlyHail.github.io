---
layout: post
title: 추석 트래픽
date: 2021-01-14 20:00:00 +0900
author: EarlyHail
categories: "Algorithm"
description: 프로그래머스 2018 카카오 1차 추석 트래픽
tags: "Algorithm"
---

## 추석 트래픽

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/17676?language=javascript)

### JavaScript

```javascript
function solution(lines) {
  var answer = 0;
  const linesArr = lines.map((line) => {
    const splitted = line.split(" ");
    const endTime = new Date(splitted[0] + " " + splitted[1]).getTime();
    const duration = parseFloat(splitted[2].slice(0, -1)) * 1000;
    const startTime = endTime - duration + 1;
    return [startTime, endTime];
  });

  // 현재 시간
  let dateIter = linesArr[0][0];
  // 가장 빨리 끝나는 작업의 끝나는 시간
  let firstEnd = undefined;
  let maxCount = 0;
  let currentCount = 0;
  let tempCount = 0;
  while (dateIter <= linesArr[linesArr.length - 1][1]) {
    // 현재시간 + 1초
    const maxIter = dateIter + 999;
    for (let i = 0; i < linesArr.length; i++) {
      const startTime = linesArr[i][0];
      const endTime = linesArr[i][1];
      if (!(endTime < dateIter) && !(maxIter < startTime)) {
        //카운트
        currentCount++;
        //끝나는시간 업데이트
        if (endTime == dateIter) {
          continue;
        }
        if (firstEnd == undefined) {
          firstEnd = endTime;
        } else {
          firstEnd = Math.min(firstEnd, endTime);
        }
      }
    }
    if (currentCount > maxCount) maxCount = currentCount;
    if (firstEnd == undefined) {
      dateIter += 1000;
    } else {
      dateIter = firstEnd;
    }
    firstEnd = undefined;
    currentCount = 0;
  }
  return maxCount;
}
```

### 접근방법

1. lines 배열을 `[시작시간, 종료 시간]` 형태로 변경한다.

2. `dateIter`는 1초의 시작시간이다.

   `dateIter`의 값이 마지막 로그의 종료 시간보다 작은 동안 반복된다.

   `dateIter`는 1초 내에서 가장 빨리 끝나는 작업의 시간으로 업데이트 된다. 없을 경우 1초를 더한다.

   예를 들어, 1.000 ~ 1.005 로그와 2.000 ~ 2.010이 있으면 dateIter는 1.000, 1.005, 2.000, 2.010 순서로 변한다.

   `maxIter`는 1초 간격의 종료를 의미한다. "처리시간은 시작시간과 끝 시간을 포함" 이므로 0.999초를 더해준다.

3. 각 `dateIter`에 대해, 모든 로그를 검사하며 1초 안에 끝나는지 검사한다.

   - `!(로그의 종료 시간보다 dateIter가 작은 경우) AND !(로그의 시작 시간보다 maxIter가 큰 경우)`를 제외하면 로그는 항상 dateIter, maxIter 사이에 처리되는 경우이다.

     해당 조건을 만족하면 count하고, `firstEnd`를 업데이트한다.

     - `firstEnd`는 `dateIter`이후 끝나는 작업들 중에서, `가장 빨리 끝나는 작업의 시간`이다.

     - `endTime == firstEnd`인 경우, firstEnd를 업데이트하지 않는다.

4. 모든 로그를 검사 하면 `dateIter`와 `maxCount`를 업데이트 한다.

   `firstEnd`가 존재하지 않은 경우 1초를 더해준다. (1초 안에 끝나는 로그가 없는 경우)

   `firstEnd`가 존재할 경우 `dateIter`에 할당한다.
