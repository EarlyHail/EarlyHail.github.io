---
layout: post
title: Map vs Object
date: 2021-01-29 02:00:00 +0900
author: EarlyHail
categories: JavaScript
description: Map과 Object를 비교해보자
tags: [JavaScript, Map, Object]
---

알고리즘 스터디를 하다가 Object로 풀면 시간초과가 나는데 Map으로 풀면 해결되는 문제가 나왔다.

이야기가 나온 김에 Map과 Object의 차이점을 정리하고 성능을 측정해보려 한다.

## Map vs Object

|                | Map                                                               | Object                                                                                                              |
| -------------- | ----------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| 의도치 않은 키 | Map은 명시적으로 제공한 키 외에는 어떤 키도 가지지 않습니다.      | Object는 프로토타입을 가지므로 기본 키가 존재할 수 있습니다. 주의하지 않으면 직접 제공한 키와 충돌할 수도 있습니다. |
| 키 자료형      | Map의 키는 함수, 객체 등을 포함한 모든 값이 가능합니다.           | Object의 키는 반드시 String 또는 Symbol이어야 합니다.                                                               |
| 키 순서        | Map의 키는 정렬됩니다. 따라서 Map의 순회는 삽입순으로 이뤄집니다. | Object의 키는 정렬되지 않습니다.                                                                                    |
| 크기           | Map의 항목 수는 size 속성을 통해 쉽게 알아낼 수 있습니다.         | Object의 항목 수는 직접 알아내야 합니다.                                                                            |
| 순회           | Map은 순회 가능하므로, 바로 순회할 수 있습니다.                   | Object를 순회하려면 먼저 모든 키를 알아낸 후, 그 키의 배열을 순회해야 합니다.                                       |
| 성능           | 잦은 키-값 쌍의 추가와 제거에서 더 좋은 성능을 보입니다.          | 잦은 키-값 쌍의 추가와 제거를 위한 최적화가 없습니다.                                                               |

출처 : [MDN-JavaScript-Map](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Map)

### 1. 의도치 않은 키

```javascript
const map = new Map();
map.set("key1", 1);
map.set("toString", 2);
console.log(map.get("toString")); // 2
console.log(map.toString()); // do what toString do

const map2 = new Map();
map2["key1"] = 1;
map2["toString"] = 2;
console.log(map2.get("toString")); // 2
console.log(map2.toString()); // error

const obj = {};
obj[] = 1;
obj["toString"] = 2;
console.log(obj["toString"]); // 2
console.log(obj.toString()); // error
```

출처 : [Stop Using Objects as Hash Maps in JavaScript](https://medium.com/better-programming/stop-using-objects-as-hash-maps-in-javascript-9a272e85f6a8)

Map의 set을 사용할 경우 제공한 키만을 사용한다. 따라서 `toString`같은 값도 키로 사용할 수 있다.

하지만 Map과 bracket(`[]`)을 사용할 경우 Object와 비슷한 현상이 일어난다. (사실 이 방법은 Map의 부적절한 사용이다.)

### 2. 키 자료형

```javascript
const map = new Map();
const user1 = { name: "HojinLee", nickname: "EarlyHail" };
const user2 = { name: "HojinLee", nickname: "EarlyHail" };
const function1 = () => true;
const function2 = () => true;
map.set(user1, 1);
map.set(function1, 2);
console.log(map.get(user1)); // 1
console.log(map.get(user2)); // undefined
console.log(map.get(function1)); // 2
console.log(map.get(function2)); // undefined

const obj = {};
const user3 = { name: "HojinLee", nickname: "EarlyHail" };
const user4 = { name: "HojinLee2", nickname: "EarlyHail2" };
obj[user3] = 1;
obj[user4] = 2;
console.log(obj[user3]); // 2
console.log(obj[user4]); // 2
console.log(obj["[object Object]"]); // 2
```

Map은 `function`과 `Object`등을 키로 사용할 수 있다.

하지만 Object는 String과 Symbol만 사용할 수 있다.

예시에서 볼 수 있는것 처럼 Object를 키로 등록하면 `"[object Object]"`가 키로 등록이 된다.
![object-key-object](/assets/posts/JavaScript/Map-vs-Object/img1.png)

### 3. 키 순서

ECMA2015(ES6)이후 Object도 키가 생성된 순서를 기억합니다.

```javascript
const map = new Map();
map.set("b", 1);
map.set("c", 2);
map.set("a", 3);
console.log(map.keys()); // [Map Iterator] { 'b', 'c', 'a' }

const obj = {};
obj.b = 1;
obj.c = 2;
obj.a = 3;
console.log(Object.keys(obj)); // [ 'b', 'c', 'a' ]
```

### 4. 크기

```javascript
const map = new Map();
map.set("b", 1);
map.set("c", 2);
map.set("a", 3);
console.log(map.size); // 3

const obj = {};
obj.b = 1;
obj.c = 2;
obj.a = 3;
console.log(Object.keys(obj).length); // 3
```

### 5. 순회

```javascript
const map = new Map();
map.set("b", 1);
map.set("c", 2);
map.set("a", 3);
const iter = map.entries();
let mapValue = iter.next();
while (mapValue.done != true) {
  const [key, value] = mapValue.value;
  console.log(`${key}: ${value}`);
  mapValue = iter.next();
}
for (let [key, value] of map) {
  console.log(`${key}: ${value}`);
}

const obj = {};
obj.b = 1;
obj.c = 2;
obj.a = 3;
Object.keys(obj).forEach((key) => console.log(`${key}: ${obj[key]}`)); // 3
```

`Object`도 entries로 [key, value]를 배열로 받아올 수 있지만 entries의 polyfill은 key를 조회한 뒤 접근하는 것이다.

## Map vs Object - 성능

길이가 20인 랜덤 문자열을 200만개 생성하여 key, value로 삽입 / 조회 테스트를 해보자

```javascript
const randomKeys = [];
const randomValues = [];
for (let i = 0; i < size; i++) {
  randomKeys[i] = makeid(20);
  randomValues[i] = makeid(20);
}

const benchmarkMap = (size) => {
  const map = new Map();
  console.time("benchmarkMapSet");
  for (let i = 0; i < size; i++) map.set(randomKeys[i], randomValues[i]);
  console.timeEnd("benchmarkMapSet");
  console.time("benchmarkMapGet");
  for (let i = 0; i < size; i++) {
    const x = map.get(randomKeys[i]);
  }
  console.timeEnd("benchmarkMapGet");
  console.time("benchmarkMapDelete");
  for (let i = 0; i < size; i++) {
    map.delete(randomKeys[i]);
  }
  console.timeEnd("benchmarkMapDelete");
};

const benchmarkObj = (size) => {
  const obj = {};
  console.time("benchmarkObjSet");
  for (let i = 0; i < size; i++) obj[randomKeys[i]] = randomValues[i];
  console.timeEnd("benchmarkObjSet");
  console.time("benchmarkObjGet");
  for (let i = 0; i < size; i++) {
    const x = obj[randomKeys[i]];
  }
  console.timeEnd("benchmarkObjGet");
  console.time("benchmarkObjDelete");
  for (let i = 0; i < size; i++) {
    delete obj[randomKeys[i]];
  }
  console.timeEnd("benchmarkObjDelete");
};

benchmarkMap(size);
benchmarkObj(size);
```

![map-obj-benchmark](/assets/posts/JavaScript/Map-vs-Object/img2.png)

Map이 Set은 약 2배, Get은 약 4배 빠른 성능을 보였고 Delete는 약간 느린 성능을 보였다.

MDN은 `Map은 잦은 키-값 쌍의 추가와 제거에서 더 좋은 성능을 보입니다.`라고 하는데... 결과가 달라서 당황스럽다

![map-obj-benchmark-small-size](/assets/posts/JavaScript/Map-vs-Object/img3.png)

`size = 10`일 경우 Object가 Set, Get에 더 빠른 성능을 보였다.

```javascript
const size = 1000000;

const benchmarkMap = (size) => {
  console.time("benchmarkMapSet");
  const map = new Map();
  for (let i = 0; i < size; i++) map.set(i, i);
  console.timeEnd("benchmarkMapSet");
  console.time("benchmarkMapGet");
  for (let i = 0; i < size; i++) {
    const x = map.get(i);
  }
  console.timeEnd("benchmarkMapGet");
  console.time("benchmarkMapDelete");
  for (let i = 0; i < size; i++) {
    map.delete(i);
  }
  console.timeEnd("benchmarkMapDelete");
};

const benchmarkObj = (size) => {
  console.time("benchmarkObjSet");
  const obj = {};
  for (let i = 0; i < size; i++) obj[i] = i;
  console.timeEnd("benchmarkObjSet");
  console.time("benchmarkObjGet");
  for (let i = 0; i < size; i++) {
    const x = obj[i];
  }
  console.timeEnd("benchmarkObjGet");
  console.time("benchmarkObjDelete");
  for (let i = 0; i < size; i++) {
    delete obj[i];
  }
  console.timeEnd("benchmarkObjDelete");
};

benchmarkMap(size);
benchmarkObj(size);
```

![map-obj-benchmark-simple-key-value](/assets/posts/JavaScript/Map-vs-Object/img4.png)

연속된 key, value 같이 간단한 key, value의 경우 Object가 성능이 더 좋았다.

간단하게 사용할 때는 Object, 참조 수나 데이터 수가 많을 때는 Map을 사용하게 될것 같다.
