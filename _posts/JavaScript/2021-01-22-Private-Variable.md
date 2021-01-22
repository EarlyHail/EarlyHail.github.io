---
layout: post
title: private 변수 만들기
date: 2021-01-22 20:00:00 +0900
author: EarlyHail
categories: JavaScript
description: JavaScript로 private한 변수를 만들어보자
tags: JavaScript
---

## class로 Private 변수 만들기

ES2019에서는 `#`를 이용해 private 필드를 선언할 수 있다.

```javascript
class Person {
  #nickname;
  constructor(name, nickname) {
    this.name = name;
    this.#nickname = nickname;
  }
  geNickname() {
    return this.#nickname;
  }
}

const person = new Person("Hojin Lee", "EarlyHail");
console.log(person.name);
// console.log(person.#nickname); // Syntax 에러, private 변수 접근
console.log(person.getNickname());
```

이전의 JavaScript에서는 어떻게 private 변수를 만들었을까???

## JavaScript의 멤버는 기본적으로 모두 공개된다.

```javascript
function Person(name, nickname) {
  this.name = name;
  this.nickname = nickname;
}

const person = new Person("HojinLee", "EarlyHail");
console.log(person.name);
console.log(person.nickname);
```

자바스크립트 객제의 멤버는 기본적으로 공개된다. (접근가능)

클로저를 이용해서 멤버가 아니라 변수를 만들어 은닉할 수 있다.

## Closure로 변수 은닉하기 (Module Pattern)

```javascript
function personFunc(initialName, initialNickname) {
  const name = initialName;
  const nickname = initialNickname;
  const getName = () => {
    return name;
  };
  const getNickname = () => {
    return nickname;
  };
  return { getName, getNickname };
}

const person = personFunc("HojinLee", "EarlyHail");
console.log(person.name); // undefined
console.log(person.nickname); // undefined
console.log(person.getName()); // HojinLee
console.log(person.getNickname()); //EarlyHail
```

## class의 private명시 없이 private 하도록

자료 조사를 하다가 신기한 방법을 발견했다. [링크](https://css-tricks.com/implementing-private-variables-in-javascript/)

```javascript
class Person {
  constructor(initialName, initialNickname) {
    const name = initialName;
    const nickname = initialNickname;
    this.getName = () => {
      return name;
    };
    this.getNickname = () => {
      return nickname;
    };
  }
}

const person = new Person("HojinLee", "EarlyHail");
console.log(person.name); // undefined
console.log(person.nickname); // undefined
console.log(person.getName()); // HojinLee
console.log(person.getNickname()); //EarlyHail
```

변수들을 private로 선헌하지 않고 생성자 내의 closure로 활용하는 방법이다.

사용하진 않을것 같다....

## closure 사용시 생길 수 있는 문제점

```javascript
function personFunc(name, nickname) {
  const info = { name, nickname };
  const getInfo = () => {
    return info;
  };
  return { getInfo };
}

const person = personFunc("HojinLee", "EarlyHail");
const personInfo = person.getInfo();
console.log(personInfo); // { name: 'HojinLee', nickname: 'EarlyHail' }

personInfo.name = "Not Hojin";
console.log(person.getInfo()); //{ name: 'Not Hojin', nickname: 'EarlyHail' }
```

객체를 저장할 경우, 객체의 key로 접근해서 값을 변경하면 변수의 값을 변경할 수 있다.

이러한 문제점은 처음에 다뤘던 ES2019의 `#`를 이용한 private 멤버 생성에서도 발생한다.

Closure 사용하는 방법 외에 private 멤버를 만드는 방법을 찾기 위해 글을 썼는데.... 찾지 못했다.

언젠가 찾게 되면 업데이트 해야지
