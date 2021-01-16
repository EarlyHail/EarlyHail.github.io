---
layout: post
title: HTML 렌더링 과정
date: 2021-01-16 21:00:00 +0900
author: EarlyHail
categories: Frontend
description: 브라우저가 HTML을 렌더링하는 과정을 알아보자
tags: Frontend
---

# 브라우저의 HTML 렌더링 과정

구글 Web Fundamentals의 "주요 렌더링 경로 최적화"와 네이버 D2의 "브라우저는 어떻게 동작하는가?"를 읽으며 정리한 글입니다.

구글 : [https://developers.google.com/web/fundamentals/performance/critical-rendering-path?hl=ko](https://developers.google.com/web/fundamentals/performance/critical-rendering-path?hl=ko)

네이버 D2 : [https://d2.naver.com/helloworld/59361](https://d2.naver.com/helloworld/59361)

### 브라우저의 기본 구조

1. 사용자 인터페이스 - 주소 표시줄을 포함한 브라우저의 UI

2. 브라우저 엔진 - 사용자 인터페이스와 렌더링 엔진 사이의 동작을 제어.

3. 렌더링 엔진 - 화면 렌더링

4. 통신 - 네트워크 호출

5. UI 백엔드 - 콤보 박스와 창 같은 기본적인 장치를 그림.

6. 자바스크립트 해석기 - 자바스크립트 코드를 해석하고 실행.

7. 자료 저장소 - 쿠키, LocalStorage 등의 자료를 저장하는 웹 데이터베이스

## 렌더링 엔진

요청받은 내용 (HTML, XML, 이미지, PDF 등)을 화면에 표시하는 일

ex) 파이어폭스의 Gecko, 사파리 / 크롬의 Webkit

### 렌더링 엔진의 동작과정

![렌더링](/assets/posts/Frontend/HTML-Rendering/Untitled.png)

### 1. HTML 파싱 - DOM 트리, CSSOM 트리 구축

- DOM (Document Object Model)

  1. 파일의 인코딩에 따라 문자를 변환한다.

  2. 토큰화 (Tokenize)

     input : 문자열 / output : HTML Token

  3. 어휘 분석 (Lexical Analyze)

     HTML 문법을 기준으로 Token을 Token 객체로 변환한다.

     ex) `<div>hi</div>`를 <, div, >, hi, <, /, div, >로 나누는건 토큰화

     div : hi로 바꾸는건 Lexer

     `위 예시의 표현은 정확하지 않을 수 있음!!!`

     input : HTML Token / output :

  4. 구문분석 (Syntax Analyze)를 통해 Lexer의 결과를 parse tree로 만든다. —> `DOM Tree`

- CSSOM (Css Object Model)

  외부 스타일 시트를 참조해서 CSS의 트리를 만드는 과정

  과정은 DOM과 동일하다.

  CSSOM 트리는 **style sheet에서 재정의 하도록 결정한 스타일만 표시한다**

### 2. 렌더 트리 구축

CSSOM과 DOM 트리를 합쳐서 `렌더 트리`를 만든다.

![Render Tree](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/images/render-tree-construction.png?hl=ko)

이미지 출처 : [Google Developers](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-tree-construction?hl=ko)

1. DOM 트리의 Root부터 각 노드를 순회한다.

   일부 노드는 표시되지 않기 때문에 렌더링 출력에 반영되지 않아 생략한다. (script, meta 등)

   CSS를 통해 숨겨지는 요소들도 생략한다 (display: none)

2. 각 노드에 적용될 CSSOM 규칙을 찾아 적용한다.

3. 노드를 콘텐츠 및 계산된 스타일과 함께 내보낸다.

렌더링 트리는 화면에 표시되는 모든 노드의 콘텐츠 및 스타일 정보를 모두 포함한다.

### 3. 렌더 트리 배치 (Layout)

기기의 뷰포트 내에서 이러한 노드의 정확한 위치와 크기를 계산

- 렌더링 트리의 Root부터 순회하며 각 요소의 정확한 위치와 크기를 캡처한다.

  정확한 위치와 크기를 캡처하는 `상자 모델`이 출력된다.

  ![상자 모델](/assets/posts/Frontend/HTML-Rendering/Untitled1.png)

  상자 모델은 개발자 도구에서도 확인할 수 있다.

- 모든 상대적인 측정값은 절대적인 픽셀로 변경된다.

- 렌더 트리는 각 요소의 레이아웃 계산, 픽셀 렌더링을 하는 `페인트 프로세스`에 대한 입력으로 처리된다.

### 4. 렌더 트리 그리기 (Painting)

정확한 위치, 크기가 계산된 렌더 트리를 전달받아 화면의 실제 픽셀로 변환하는 작업

### 정리

1. HTML 마크업 처리 후 DOM 트리 생성

2. CSS 마크업 처리 후 CSSOM 트리 생성

3. DOM + CSSOM = Render Tree

4. 렌더 트리 배치(레이아웃) 실행. 정확한 위치 크기 계산

5. 화면에 그리기

- 위 과정은 `점진적으로 진행된다`

  HTML 전체가 1 → 2 → 3 → 4 → 5로 진행되는게 아니라 HTML의 부분에 대해 과정이 진행된다.=
