---
layout: post
title: 가장 긴 팰린드롬
date: 2021-01-12 21:17:00 +0900
author: EarlyHail
categories: "Algorithm"
description: "프로그래머스 연습문제 Level3 가장 긴 팰린드롬 JavaScript"
tags: "Algorithm"
---

## 가장 긴 팰린드롬

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/12904?language=javascript)

## 첫 번째 시도

### JavaScript

```javascript
function solution(s) {
  let padding = 0;
  let maxPal = 1;
  for (let i = 0; i < s.length; i++) {
    while (true) {
      const result = isPal(s, i, padding);
      if (result == false) break;
      maxPal = Math.max(result, maxPal);
      padding++;
    }
    padding = 0;
  }
  return maxPal;
}

const isPal = (s, index, padding) => {
  let leftIndex = index;
  let rightIndex = index;
  while (s[index] == s[leftIndex - 1]) {
    leftIndex--;
  }
  while (s[index] == s[rightIndex + 1]) {
    rightIndex++;
  }
  if (leftIndex - padding < 0 || rightIndex + padding > s.length - 1)
    return false;
  let i;
  for (i = 1; i <= padding; i++) {
    if (s[leftIndex - i] != s[rightIndex + i]) return false;
  }
  return rightIndex - leftIndex + 1 + 2 * (i - 1);
};
```

- 효율성 1개 실패

### 접근방법

1. 문자열의 캐릭터에 대해 `isPal`함수를 실행시키고 isPal이 성공할 떄 마다 padding을 늘려가며 가장 긴 Palindrome을 찾는다.

2. `isPal`은 index와 padding값을 받아 해당 패딩까지 Palindrome 조건을 만족하는지 검사한다.

3. 검사하기 전에 짝수인 Palindrome이 있을 수 있으므로 index를 `leftIndex`와 `rightIndex`로 나눠 같은 문자열이 나올 때 까지 index를 넓힌다.

   - 예시로, `"abbbba"`이면 leftIndex는 첫 b의 인덱스(1), rightIndex는 마지막 b의 인덱스(4)가 된다.

- 효율성 테스트에서 실패한다. `"abcba"`일 때, `"bcb"`에 대해 검사하고, `"abcba"`에 대해 또 검사해서 중복 연산이 발생한다.

- 입력된 padding에 대해서만 검사하는 방법과, `isPal`함수 자체를 그 문자가 포함된 Palindrome의 최대 길이를 리턴하도록 바꾸는 방법 두 가지가 생각났는데 갈아엎고싶어서 후자를 선택했다.

## 두 번째 시도

### JavaScript

```javascript
function solution(s) {
  let maxPal = 1;
  for (let i = 0; i < s.length; i++) {
    const result = isPal(s, i);
    maxPal = Math.max(result, maxPal);
  }
  return maxPal;
}

const isPal = (s, index) => {
  let leftIndex = index;
  let rightIndex = index;
  while (s[index] == s[leftIndex - 1]) {
    leftIndex--;
  }
  while (s[index] == s[rightIndex + 1]) {
    rightIndex++;
  }
  let i = 0;
  while (leftIndex - i >= 0 && rightIndex + i < s.length) {
    if (s[leftIndex - i] != s[rightIndex + i]) break;
    i++;
  }
  return rightIndex - leftIndex + 1 + 2 * (i - 1);
};
```

### 접근방법

1. isPal함수는 각 index에 해당 문자를 중심으로 만들 수 있는 최대 길이의 Palindrome을 리턴한다.
