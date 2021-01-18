---
layout: post
title: 2018 KAKAO BLIND RECRUITMENT 자동완성
date: 2021-01-18 23:30:00 +0900
author: EarlyHail
categories: "Algorithm"
description: 프로그래머스 2018 KAKAO BLIND RECRUITMENT Level3 자동완성 JavaScript
tags: "Algorithm"
---

## 자동완성

[문제 링크](https://programmers.co.kr/learn/courses/30/lessons/17685)

### JavaScript

```javascript
function solution(words) {
  let answer = 0;
  const trie = new Trie();
  for (let i = 0; i < words.length; i++) {
    trie.insert(words[i]);
  }
  for (let i = 0; i < words.length; i++) {
    answer += trie.searchCount(words[i]);
  }
  return answer;
}

class Node {
  constructor(character) {
    this.character = character;
    this.child = {};
    this.isWord = false;
  }
}

class Trie {
  constructor() {
    this.node = new Node();
  }
  insert(word) {
    let currentNode = this.node;
    for (let i = 0; i < word.length; i++) {
      //해당 알파벳 자식이 없으면 만들어줌
      if (currentNode.child[word[i]] == undefined) {
        const newNode = new Node(word[i]);
        currentNode.child[word[i]] = newNode;
        currentNode = newNode;
        //자식이 있으면 이동
      } else {
        currentNode = currentNode.child[word[i]];
      }
    }
    //삽입이 끝난 마지막 leaf Node에 단어임을 표시
    currentNode.isWord = true;
  }
  searchCount(word) {
    let totalCount = 0;
    let countAfterWord = 0;
    let currentNode = this.node;
    for (let i = 0; i < word.length; i++) {
      currentNode = currentNode.child[word[i]];
      totalCount++;
      const keyNum = Object.keys(currentNode.child).length;
      if (currentNode.isWord == true && keyNum >= 1) {
        countAfterWord = 0;
      } else if (keyNum >= 2) {
        countAfterWord = 0;
      } else {
        if (currentNode.isWord != true) {
          countAfterWord++;
        }
      }
    }
    return totalCount - countAfterWord;
  }
}
```

- 소요시간 : 40분

### 접근방법

1. 각 입력 문자를 Trie 구조로 저장한다.

   Trie의 각 Node에는 현재 문자(Character), 자식 노드(key: character, value: Node), 어떤 단어의 마지막 글자인지 (isWord) 저장한다.

   예제 1에 대한 Trie 생성 결과

   ```json
   {
     "node": {
       "child": {
         "g": {
           "character": "g",
           "child": {
             "o": {
               "character": "o",
               "child": {
                 "n": {
                   "character": "n",
                   "child": {
                     "e": { "character": "e", "child": {}, "isWord": true }
                   },
                   "isWord": false
                 }
               },
               "isWord": true
             },
             "u": {
               "character": "u",
               "child": {
                 "i": {
                   "character": "i",
                   "child": {
                     "l": {
                       "character": "l",
                       "child": {
                         "d": { "character": "d", "child": {}, "isWord": true }
                       },
                       "isWord": false
                     }
                   },
                   "isWord": false
                 }
               },
               "isWord": false
             }
           },
           "isWord": false
         }
       },
       "isWord": false
     }
   }
   ```

2. Trie의 search는 해당 글을 찾는데 타이핑해야하는 문자의 수를 반환한다.

   - `totalCount`는 전체 검색 횟수(글자 길이)이다.

   - `countAfterWord`는 자동완성이 된 이후의 글자 수 이다.

     카운트(countAfterWord)를 셀 때

     1. 단어이고 자식이 1개 이상일 경우는 카운트를 초기화 한다.

        해당 글자가 단어이면서 마지막 글자가 아닌 경우, 자동완성이 되지 않는다.

     2. 자식이 2개 이상일 경우 카운트를 초기화 한다.

        자식이 2개 이상이면 자동완성이 되지 않는다.

     3. 위의 두 경우가 아니면서 단어가 아닐경우, 카운트를 추가한다.

        위 두 경우가 아니면서 단어인 경우는 검색하는 단어의 끝까지 검색했을 때 이다.

        자동완성을 지원하는 길이를 찾아야 하므로 마지막 단어는 카운트를 하면 안된다.(1개를 더 입력해야 타이핑이 되므로)

     예를 들어, 예제 3에서 `war` 또는 `warrior`을 검색할 때

     - `w`는 `word`, `war`, `warrior`에 의해 자식이 2개 이상이기 때문에 카운트를 초기화한다.

     - `a`는 1, 2번 조건에 맞으므로 카운터를 추가한다.

     - `r`을 만났을 때, `war`를 검색하는 경우 단어이면서 자식이 존재하기 때문에 자동완성이 안된다. 따라서 카운트를 0으로 초기화 한다.

     - `r`을 만났을 때, `warior`를 검색하는 `war`때문에 카운터를 초기화 해준다.

     - 두번째 `r`, `i`, `o`, `r`를 만나면 1, 2번이 아니고 단어가 아니므로 카운트를한다. 이 때 마지막 단어는 카운트를 하지 않는다.

3. 문자를 순회하며 search함수를 적용하고 결과를 더해준다.
