---
layout: post
title: 오픈채팅방
date: 2021-01-16 22:30:00 +0900
author: EarlyHail
categories: "Algorithm"
description: 프로그래머스 연습문제 Level2 오픈채팅방 JavaScript
tags: "Algorithm"
---

## 오픈채팅방

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/42888)

### JavaScript

```javascript
const actionType = {
  Enter: "들어왔습니다.",
  Leave: "나갔습니다.",
};
const actionType = {
  Enter: "들어왔습니다.",
  Leave: "나갔습니다.",
};
function solution(record) {
  const uidActions = [];
  const answer = [];
  const userObj = {};
  for (let i = 0; i < record.length; i++) {
    const [action, uid, userName] = record[i].split(" ");
    if (action == "Change") {
      userObj[uid] = userName;
    } else if (action == "Enter") {
      userObj[uid] = userName;
      uidActions.push([uid, actionType[action]]);
    } else if (action == "Leave") {
      uidActions.push([uid, actionType[action]]);
    }
  }
  for (let i = 0; i < uidActions.length; i++) {
    const [uid, actionStr] = uidActions[i];
    const userActionStr = `${userObj[uid]}님이 ${actionStr}`;
    answer.push(userActionStr);
  }
  return answer;
}
```

- 소요시간 : 15분

### 접근방법

1. 모든 사용자를 닉네임이 아닌 uid로 저장한다.

2. 사용자의 uid와 닉네임이 각각 key-value인 자료구조로 저장한다.

3. 사용자 데이터와 사용자의 action 데이터를 각각 저장 후, action 데이터를 출력할 때 사용자의 닉네임을 가져와 출력한다.

- 함정은 uid가 항상 `uid`로 시작하지 않는다는거다!!!

  조건에 없었는데 너무 당연한것처럼 uid를 지우고 해서 찾는데 꽤 걸림...
