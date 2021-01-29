---
layout: post
title: "인터넷 연결부터 웹 페이지 접속까지"
date: 2021-01-09
author: EarlyHail
categories: Network
description: "인터넷 연결부터 웹 페이지에 접속하기 까지 무슨 일이 일어나는지 알아보자"
tags: Network
---

- 이 자료는 [Computer Networking: A Top-Down Approach](https://www.pearson.com/us/higher-education/program/Kurose-Computer-Networking-A-Top-Down-Approach-7th-Edition/PGM1101673.html)를 참고하여 작성되었습니다.

주소창에 사이트 이름을 친 이후부터, 웹 페이지 파일을 받을 때 까지 무슨 일이 일어나는지 정리해보자.

![](/assets/posts/Network/Process-of-Access-Website/Untitled0.png)

## Client IP 구하기

운영체제는 컴퓨터의 IP, Subnet Mask, Gateway 주소를 관리한다.

![](/assets/posts/Network/Process-of-Access-Website/Untitled1.png)

만약 IP 주소가 없다면?

Client는 DHCP 서버로부터 사용 가능한 IP를 받아온다.

## DHCP

(Dynamic Host Configuration Protocol : 동적 호스트 구성 프로토콜)

- ISP로부터 할당받은 공인 IP들을 기관 내부의 호스트에 할당한다.
- 기관 내부의 호스트의 수보다 할당받은 공인 IP의 주소 블록이 더 적기 때문에 LifeTime을 설정한다.
- 자동으로 주소를 할당받는 Plug-and-Play를 지원한다.

1. DHCP Discover - Client : DHCP 서버 찾습니다!!!

   출발지 IP 0.0.0.0, broadcast IP 255.255.255.255로 설정한 뒤 자신의 MAC 주소와 broadcast MAC 주소 FF-FF-FF-FF-FF-FF로 링크 계층 프레임을 생성한 뒤 전송한다.

2. DHCP offer - Server : IP 드릴께요!!!

   DHCP 서버는 Broadcast 된 프레임을 전송 받아 IP 데이터그램을 추출하고 DHCP 메시지를 확인 후 IP를 할당하여 호스트의 IP, 게이트웨이 IP, 서브넷마스크, DNS 서버의 IP 등의 정보를 포함시켜 DHCP ACK 메시지를 링크 계층 프레임으로 캡슐화 하여 호스트에게 보낸다.

3. DHCP Request - Client : 네 쓸게요!!!!

4. DHCP ACK - Server : OK

- 3, 4 과정에서 Client가 사용할 정보들이 같이 전송된다.

  ![](/assets/posts/Network/Process-of-Access-Website/Untitled2.png)

## 이제 내 IP가 있으니, 요청을 보내보자.

먼저 보내는 요청은 페이지에 대한 요청이 아닌 TCP 연결 요청이다.

### 3-way handshaking

TCP는 연결형 프로토콜로, 데이터를 `전송하기 이전`에 3단계에 걸처 Handshaking으로 두 Host를 연결한다.

3번에 요청을 걸쳐 TCP Connection이 맺어지면 사용자는 HTTP 요청을 보내고 요청에 따른 자원을 받아온다. TCP에 대해서 자세히 다루지는 않겠다.

### Application 부터 Data Link 까지

Application 계층부터 Data Link 계층까지 `데이터가 정제`되고 `필요한 정보들을 프로토콜을 이용하여 받아오는` 과정을 따라가보자

- Application Layer - 구글 주소를 입력하고 엔터를 누르면 HTTP Request Data가 만들어진다.

  ![](/assets/posts/Network/Process-of-Access-Website/Untitled3.png)

- Transport Layer - HTTP Request를 Payload에 넣고, Source 의 port와 Destination의 port를 추가한 Datagram을 만든다.

  ![](/assets/posts/Network/Process-of-Access-Website/Untitled4.png)

- Network Layer - Datagram에 Source IP 주소, Destination의 IP 주소를 추가한 Packet을 만든다.

  `Destination IP`주소는 `DNS`를 이용하여 받아온다.

  ![](/assets/posts/Network/Process-of-Access-Website/Untitled5.png)

## DNS

DNS : Domain Name Service

도메인 주소(e.g. naver.com)을 ip 주소 (e.g. 210.89.160.88)으로 변경시켜주는 서비스

1. 요청하는 호스트에 등록된 local DNS 서버에게 목적지 호스트 주소의 IP를 물어본다.

2. local DNS의 캐시에 있으면 바로 알려주고 없으면 상위 계층 DNS 서버인 root DNS 서버에게 질의한다.

3. root DNS 서버는 도메인 이름을 관리하는 TLD(Top level domain : 최상위) DLS 서버의 주소를 알려준다.

4. local DNS 서버는 TLD DNS 서버에게 호스트 주소의 IP를 질의하여, 책임 DNS 서버(목적지의 로컬 DNS 서버)의 주소를 받는다.

5. local DNS 서버는 책임 DNS 서버에 호스트의 IP를 질의하여 얻은 후 자신의 캐시에 등록하고 요청한 호스트에게 돌려준다.

   ![](/assets/posts/Network/Process-of-Access-Website/Untitled6.png)

- Link Layer - Packet에 MAC 주소를 추가한 Frame을 만든다.

  ![](/assets/posts/Network/Process-of-Access-Website/Untitled7.png)

여기서 Destination MAC Address는 요청의 목적지 Host의 주소가 아닌, 해당 패킷이 처음 거쳐갈 네트워크 장비의 목적지 주소이다!!!

## MAC Address

MAC address (a.k.a LAN or 물리 or Ethernet 주소)

48bit의 값으로 이루어진, 전 세계의 모든 LAN Card에 붙여진 Unique한 주소

### IP Addr VS MAC Addr

- IP주소는 네트워크 계층의 주소

  IP주소는 목적지까지의 경로를 찾기 위해 사용

- MAC Addr는 링크 계층의 주소

  MAC 주소는 링크 하나를 통과하기 위해서 사용 (같은 서브넷에서 데이터를 전달하기 위해 사용)

![](/assets/posts/Network/Process-of-Access-Website/Untitled8.png)

ex) A가 H한테 Data를 보냄

- IP : A에서 보낸 Data가 H까지 도착하기 위해 사용됨

- MAC : A-S1, S1-S4, S4-S3, S3-H로 데이터가 이동할 수 있도록 사용됨

## ARP : MAC 주소를 얻는 프로토콜

Address Resolution Protocol

1. Client는 Broadcast를 이용하여 IP를 가지고 MAC 주소를 질의한다.

   ![](/assets/posts/Network/Process-of-Access-Website/Untitled9.png)

2. 해당 IP주소를 가진 Host는 자신의 Mac 주소를 알려준다.

   ![](/assets/posts/Network/Process-of-Access-Website/Untitled10.png)

## Router

라우터의 역할

1. IP를 확인하여 가장 가까운 길에 있는 다른 라우터 선택 (Routing)

2. 해당 라우터로 패킷 전달 (forwarding)

라우터는 Frame (Link 계층)에서 Packet(Network 계층)을 추출, Forwarding Table에서 IP 주소를 찾아 해당 주소로 가는 라우터로 가는 Frame 만들어 다음 라우터에 전송한다.

라우팅 프로토콜에 따라 속도 저하를 막기 위해 Frame을 만들지 않고 `Packet 상태로` IP주소만을 이용해 라우팅하기도 한다.

Router를 따라 요청이 서버에 도착하면 같은 절차를 통해 TCP Connection을 맺고, 사용자는 HTTP 요청을 보내고 이에 대한 응답으로 페이지의 정보를 받아 브라우저에 보내준다.
