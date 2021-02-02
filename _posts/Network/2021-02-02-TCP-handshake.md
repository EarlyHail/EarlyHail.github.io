---
layout: post
title: TCP의 handshake
date: 2021-02-02 22:00:00 +0900
author: EarlyHail
categories: Network
description: TCP의 3-way / 4-way handshake와 왜 3-way 대신 2-way handshake는 안되는지 알아보자
tags: [Network, TCP]
---

### TCP의 Handshake란?

Data를 전송하기 전에 송신자와 수신자는 Handshake를 통해 `connection을 맺는것에 대한 동의`를 하고 `연결에 필요한 parameter에 대한 동의`를 한다.

여기서 parameter는 `Flow Control`에 필요한 Window Size나 `신뢰적 전송`에 필요한 Sequence Number, `전송 크기를 결정`하기 위한 MSS(Maximum Segment Size) 등이 있다.

또한 연결을 종료하기 전에 `connection을 종료하는 것에 대한 동의`를 위해 handshake를 진행한다.

## 3-way handshake

### 3-way handshake란?

송수신자 간의 연결을 맺기 위해 총 3번의 전송이 발생하기 때문에 3-way handshake라고 불린다.

![3-way handshake](/assets/posts/Network/TCP-handshake/img1.png)

이미지 출처 : [Building Blocks of TCP](https://hpbn.co/building-blocks-of-tcp/)

1. 클라이언트는 자신의 Sequencial Number를 결정하여 `SYN` 패킷을 보낸다.

2. 서버는 클라이언트의 Sequence Number를 확인하고 자신의 Sequence Number를 결정하여 `SYNACK` 패킷을 보낸다.

3. SYNACK 패킷을 받은 클라이언트는 `ACK` 패킷을 보내며 연결을 완료한다. 이때 보낼 데이터가 있으면 함께 보낸다.

### Sequence Number란?

전송해야할 데이터를 바이트 단위로 표시한 번호이다.

간단히 말하면 `나 seq번째 데이터부터 보낼께`이다.

MSS가 1500이고, 보내야 할 데이터가 4500바이트 라면 seq는 0, 1500, 3000이 된다.

전송해야할 데이터 뿐만 아니라 받은 데이터를 표시한 번호도 있는데, `Ack Number`이다.

Ack은 `나 ack번째 데이터까지 받았어`가 되고 위 상황에서는 1500, 3000, 4500이 된다.

이 두 번호를 통해 데이터가 중간에 소멸되었을 경우 원래 데이터를 얻어내기 위한 행동을 취하게 된다. (오류가 발생한 비트를 고칠 수 도 있지만 대부분 재전송을 시행한다)

### 왜 2-way handshake는 안될까?

위 3-way handshake의 3번째 과정을 생략할 수 있을지 생각해보자.

TCP는 연결한 두 개체 모두 데이터를 전송할 수 있다. 따라서 서로 어디부터 전송할지 (Sequence Number)에 대한 약속이 필요하다.

따라서 서버가 2번째 과정에서 전송한 Sequence Number를 확인했는지 확인 해야하기 때문에 3번의 handshake 과정이 필요하다

### 그럼 왜 Sequence Number는 랜덤일까?

그럼 왜 굳이 Sequence Number를 랜덤으로 결정해서 3-way를 실행할까에 대한 의문이 들 수 있다.

Sequence Number를 0부터 시작하게 되면 문제가 생기는데 동일한 TCP 포트로 연결을 새롭게 생성했는데 이전 연결에서 도달하지 못했던 패킷이 있다면 해당 패킷은 새로운 연결의 데이터로 인식 될 가능성이 있다.

또한 보안적인 문제도 발생할 수 있다고 한다.

## 4-way handshake

송수신자 간의 연결을 종료하기 위해 총 4번의 전송이 발생하기 때문에 4-way handshake라고 불린다.

![4-way handshake](/assets/posts/Network/TCP-handshake/img2.png)

이미지 출처 : [https://getchan.github.io/cs/network_5/](https://getchan.github.io/cs/network_5/)

1. 클라이언트는 자신은 데이터를 다 보냈고, 연결을 종료하길 원한다고 `FIN`패킷을 보낸다.

2. 서버는 1번에 대한 응답으로 `ACK`패킷을 보내고, 자신이 보내야 하는 나머지 데이터를 마저 전송한다.

3. 서버는 서버가 보내야 할 데이터를 다 보내면, 연결을 종료하길 원한다고 `FIN`패킷을 보낸다.

4. 클라이언트는 3번에 대한 응답으로 `ACK`패킷을 보내고, 두 노드 모두 종료하기로 동의하였으므로 TCP 연결을 종료한다.
