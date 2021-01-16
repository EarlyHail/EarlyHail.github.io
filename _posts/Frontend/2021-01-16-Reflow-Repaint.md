---
layout: post
title: Reflow & Repaint
date: 2021-01-16 22:00:00 +0900
author: EarlyHail
categories: Frontend
description: Reflow & Repaint
tags: Frontend
---

# Reflow & Repaint

### 브라우저 동작 과정

1. HTML 마크업 처리 후 DOM 트리 생성
2. CSS 마크업 처리 후 CSSOM 트리 생성
3. DOM + CSSOM = Render Tree
4. 렌더 트리 배치(레이아웃) 실행. 정확한 위치 크기 계산 (Layout)
5. 화면에 그리기 (Painting)

# Reflow

- Layout 과정을 다시 수행 (브라우저 동작 과정 중 4단계)
- HTML 요소의 크기, 위치, 레이아웃 수치를 수정하는 등의 기하학적 형태를 변경할 때 발생

  Render Tree와 각 요소들의 크기와 위치를 다시 계산하는 Layout 과정을 다시 수행하고 Painting 과정을 수행한다.

### Reflow가 발생하는경우

- DOM 요소의 위치, 크기 변경
- 브라우저의 크기 변경
- 사용자 행동에 따른 변화 (hover, input, scroll 등......)
- offset 또는 client 속성을 접근할 때

JavaScript에서 속성을 접근하거나 변경할 때 Reflow가 발생하는 경우가 많다.

[여기](https://gist.github.com/paulirish/5d52fb081b3570c81e3a) 에서 목록을 확인할 수 있다.

### Reflow 최적화

- 가능한 단말 요소의 스타일을 변경하기 변경하기

  영향을 받는 요소들을 최소화 하여 Reflow의 수행 대상을 줄인다.

- 인라인 스타일 보다는 클래스 사용

  style 속성을 변경할 때 마다 리플로우가 발생하므로 지양해야 한다.

  클래스 추가로 css를 적용하면 성능 뿐만 아니라 가독성도 좋아진다.

- 애니메이션이 들어간 노드를 분리하기

  애니메이션이 들어간 노드의 position 속성을 `absolute` 또는 `fixed`로 설정하여 노드에서 분리하면 애니메이션 진행 시 해당 노드만 Reflow가 발생한다.

# Repaint

- Painting 과정을 다시 수행 (브라우저 동작 과정 중 5단계)
- `background-color`, `visibility`, `outline`, `border`등 레이아웃에 영향을 주지 않는 속성이 변경될 때 Repaint가 진행된다.

  ex) 배경 색을 변경한다고 HTML 요소의 위치, 크기를 다시 계산할 필요는 없기 때문에 Repaint만 발생한다.
