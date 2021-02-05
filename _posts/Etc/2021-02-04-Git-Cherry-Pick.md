---
layout: post
title: Git CherryPick에 대해 알아보자
date: 2021-02-04 17:00:00 +0900
author: EarlyHail
categories: Etc
description: Git CherryPick에 대해 알아보자
tags: [Git, CherryPick]
---

### Cherry Pick이란?

특정 커밋의 `변경사항만` 가져오는 것이다.

## 예시로 알아보기

```
현재의 커밋 로그는 다음과 같다.
commit 169acbcdb9bf844ca33d58d82f9be63d2556f24a (HEAD -> master)

    fifth commit

commit 78a6496b9704d478e47a5dde918125397fceafb0

    fourth commit

commit 37c1f1a44e2d0b1d304161a76cdf5b3bc7160488

    third commit

commit 288e115c4e0e1594ba590156b4e3d6c4365ae3d8

    second commit

commit 5ec468603ab8b03a638ed29a5c7cf866c496377d

    first commit
```

![cherrypick-example-commits](/assets/posts/Etc/Git-Cherry-Pick/img1.png)

5개의 커밋이 있는 상황에서, 1, 2, 4번 커밋만 적용하고 싶을 떄 어떻게 해야할까?

### Cherry Pick을 이용하기

먼저 `git checkout 5ec468603ab8b03a638ed29a5c7cf866c496377d`로 처음 커밋으로 돌아간다.

`git checkout -b cherrypick`으로 새로운 브렌치를 하나 만들어준다.

이후 2번, 4번 커밋에 대해 cherry pick을 적용시킨다.

```bash
git cherry-pick 288e115c4e0e1594ba590156b4e3d6c4365ae3d8 78a6496b9704d478e47a5dde918125397fceafb0
```

![cherrypick-example](/assets/posts/Etc/Git-Cherry-Pick/img2.png)

cherry pick으로 만들어진 브렌치는 내용은 같지만 commit로그는 다르다.

추가로, `git rebase --interactive`를 이용하는 방식도 있다고 한다...
