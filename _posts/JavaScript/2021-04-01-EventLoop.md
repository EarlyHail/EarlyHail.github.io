---
layout: post
title: EventLoop
date: 2021-04-01 15:00:00 +0900
author: EarlyHail
categories: JavaScript
description:
tags: [JavaScript]
---

자바스크립트는 **Single Thread** 언어이다.

Single Thread 언어이면 Call Stack이 하나라는 말인데.... 어떻게 비동기 처리를 하는걸까???

JavaScript 자체로는 해결할 수 없다. Browser나 Node.js의 API 도움을 받아야 한다.

![event-loop](/assets/posts/JavaScript/EventLoop/img1.png)
출처: [How JavaScript works: an overview of the engine, the runtime, and the call stack](https://blog.sessionstack.com/how-does-javascript-actually-work-part-1-b0bacc073cf)

위 이미지에서 볼 수 있는것 처럼 JavaScript는 API, EventLoop, Callback Queue와 분리되어 자신의 Call Stack을 유지하고 실행시키기만 한다.

### Web API

웹 개발을 위해 브라우저에서 다양한 API가 제공된다.

여기에는 `window`와 `element`에 있는 DOM 조작 함수를 포함한 다양한 함수들이 있으며 fetch, setTimeout 등 비동기 처리를 위한 함수들이 제공된다.

Web API는 실행되야 하는 callback을 `Callback Queue`에 삽입한다.

### Callback Queue

비동기로 실행되길 기다리는 함수들이 대기하는 Queue이다.

이 함수들은 Event Loop에 의해 JavaScript의 Call Stack으로 이동한다.

Callback Queue에는 3개의 Queue가 존재한다.

- Task Queue

  아래 두 가지 경우를 제외한 일반적인 비동기 처리들에 대해 사용되는것 같다.

  setTimeout, setInterval, setImmediate 등이 표함된다.

- Microtask Queue

  코드로 인해서만 생성되고 비동기를 동기적으로 처리할 필요가 있을 때 사용된다.

  대표적으로 Promise는 Microtask Queue이다.

- Animation Frames

  requestAnimationFrame에 의해 추가되는 비동기 함수

### Event Loop

Call Stack과 Task Queue의 상태를 확인하여 Call Stack이 비어있고 Callback Queue에 콜백 함수가 존재하면

Callback Queue에서 함수를 꺼내 Call Stack에 삽입한다.

자연스럽게 JavaScript는 Call Stack의 맨 위에 있는 함수를 실행시키며 비동기 함수가 실행된다.

EventLoop의 동작이 이해가 안되면 [https://www.youtube.com/watch?v=8aGhZQkoFbQ&t=811s](https://www.youtube.com/watch?v=8aGhZQkoFbQ&t=811s)이 영상을 강력 추천한다.
