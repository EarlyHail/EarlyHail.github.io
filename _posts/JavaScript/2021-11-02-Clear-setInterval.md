---
layout: post
title: clearInterval과 setInterval의 콜백
date: 2021-11-02 21:00:00 +0900
author: EarlyHail
categories: JavaScript
description: clearInterval은 setInterval로 등록된 콜백도 종료 시킬까?
tags: [JavaScript]
---

setInterval 관련 코드에 대해 이야기를 하다가 동료분이 아래 내용을 궁금해 하셨다.

처음엔 너무 당연할것 같았는데.... 왜? 를 생각해보니 잘 모르겠었다.

어떻게 이런 고민을 하신건지 참 대단하다 ㄷㄷ 진짜 `왜`는 중요한것 같다.

### setInterval 동작의 의문점

`setInterval(func, [delay, arg1, arg2, ...]);`은 delay마다 반복적으로 함수를 호출한다.

`setInterval`에 대해 위처럼 정의한다면 내용은 이해를 못할수도 있다....

조금만 더 디테일하게 들어가보면 `setInterval`은 브라우저 환경에서 Web API에 의해 delay 길이의 타이머가 실행되고 타이머가 종료되면 Callback Queue(Task Queue)에 콜백함수가 등록된다. 그리고 다시 타이머를 실행(반복)한다.

이런 상황에서, 아래 코드를 봐보자

```js
const handle = setInterval(() => {
  console.log("interval callback!");
}, 100);
takesTenSecs();
clearInterval(r);
```

위 코드의 실행 순서를 살펴보면

1. setInterval에 의해 타이머가 시작되고 해당 timer ID를 handle 변수에 저장한다.

2. 10초 걸리는 작업을 한다.

3. interval ID를 이용하여 clearTimer를 종료시킨다.

이 때 **\"interval callback!\"**은 몇번 출력될까?

당연하게 출력되지 않는다고 생각했다. 일단 메인 CallStack이 10초가 걸리는 `takesTenSecs()`작업 직후 clearInterval을 하기 때문에 setInterval의 콜백이 실행되지 않기 때문이다.

한번 더 나가서, **왜 setInterval의 콜백이 실행되지 않지???**를 생각해보자

### clearInterval 이후 setInterval의 콜백이 실행되지 않는 이유

처음에는 clearInterval은 CallStack에 있는 모든 callback함수를 찾아서 삭제할거라고 생각했다.

답을 알기 위해 두 함수의 명세를 확인해보자.

- `setInterval`의 spec은 다음과 같다.

  `handle = self.setInterval(handler [, timeout [, ...arguments ] ])`

  Schedules a timeout to run handler **every timeout milliseconds**. Any arguments are passed straight through to the handler.

- `clearInterval`의 spec은 다음과 같다.

  `self.clearInterval(handle)`

  Cancels the timeout set with setInterval() or setTimeout() identified by handle.

두 함수 모두 단순하게 handler(callback) 함수를 등록, 등록된 ID(handle)을 이용하여 더이상 handler(callback)이 등록되지 않도록 타이머를 종료한다.

clearInterval은 CallStack에 있는 삭제하는 동작을 하지 않는다. 위 예제 코드의 경우에 10초동안 0.1초마다 setInterval의 콜백이 Callback Queue에 쌓인다.

그렇다면 왜 쌓여있는 Callback은 실행되지 않는걸까?

### callback의 실행 조건

setInterval의 callback은 단순히 callback 함수로 등록되는게 아닌, 자바스크립트가 실행할 수 있는 단위?인 Task로 만들어진다.

Task는 task를 완료하기 위한 `steps`와, `source`, `document`, `environment setting objects의 set`으로 이루어져있다고 하는데.... 아마 Task Queue에 대한 내용 같다. (이 부분까지 디테일하게는 잘 모르겠다)

어찌됬든 타이머가 종료되고 Callback Queue(Task Queue)에 등록될 task를 완료하기 위한 steps의 첫 번째는 다음과 같다.

**If the entry for handle in the list of active timers has been cleared, then abort these steps.**

active timer의 리스트에서 handle(timer ID)가 지워져있을 경우, 실행하지 않는다.

콜백함수가 실행될 때 timer ID를 검사하고 지워져있을 경우에 실행되지 않기 때문에 메인 CallStack이 비어있지 않는기간동안 몇개의 callback 함수가 쌓여있더라도 실행되지 않는 것이다!

**Reference**

[JWT introduction](https://jwt.io/introduction)
