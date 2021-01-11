---
layout: post
title: "댓글 기능 만들기"
date: 2021-01-11 22:00:00 +0900
author: EarlyHail
categories: Jekyll
description: "DISQUS를 이용한 Jekyll 댓글 기능 만들기"
tags: Jekyll
---

## 댓글을 추가해 볼까요???

Jekyll은 정적 파일만 호스팅 해주기 때문에 댓글을 추가하기 위해서는 보통 `외부 라이브러리의 도움`을 받습니다.
댓글 DB를 만들고 JavaScript를 이용하여 렌더링하면 되긴 할것 같지만...... 굳이 그렇게 하고싶지는 않네요 :)
`Jekyll 댓글`을 검색하면 가장 많이 나오는 DISQUS를 사용해서 댓글 기능을 추가해보겠습니다.

## DISQUS 등록하기

1. 회원가입

   [DISQUS 사이트](https://disqus.com/)에서 회원가입을 해줍니다.

2. Site 생성

   ![Site 생성](/assets/posts/jekyll/Add-Comment/Disqus1.png)

   회원가입 후 나오는 화면에서, `I want to install Disqus on my site`를 클릭합니다.

3. 사이트 정보 입력

   ![Site 정보 입력](/assets/posts/jekyll/Add-Comment/Disqus2.png)

   사이트에 필요한 정보들을 입력해줍니다.

4. 플랫폼 선택

   ![플랫폼 선택](/assets/posts/jekyll/Add-Comment/Disqus3.png)

   이제 플랫폼을 선택할 차례입니다.

   Jekyll에 DISQUS를 연결시켜야 하므로, Jekyll을 선택해줍니다.

## DISQUS 적용하기

플랫폼을 선택하면 적용 방법 화면이 나옵니다.

![DISQUS 적용 방법](/assets/posts/jekyll/Add-Comment/Disqus4.png)

시키는대로 적용시켜 볼까요???

1.  `comments: true` 적용

    댓글을 활성화 시키기 위해 `comments: true` 옵션을 적용시켜야 합니다.

    post들의 레이아웃을 잡아주는 `_layouts/post.html`에 옵션을 추가해줍니다.

    ![comment 옵션](/assets/posts/jekyll/Add-Comment/Disqus5.png)

2.  `Universal Embed Code` 적용

    이제 DISQUS에서 댓글 정보를 받아와서 화면에 렌더링 해주는 코드를 추가해봅시다.

    ```html
    <div id="disqus_thread"></div>
    <script>
      (function () {
        // DON'T EDIT BELOW THIS LINE
        var d = document,
          s = d.createElement("script");
        s.src = "https://yourSiteShortName.disqus.com/embed.js";
        s.setAttribute("data-timestamp", +new Date());
        (d.head || d.body).appendChild(s);
      })();
    </script>

    <noscript
      >Please enable JavaScript to view the
      <a href="https://disqus.com/?ref_noscript"
        >comments powered by Disqus.</a
      ></noscript
    >
    ```

    가이드에서는 위 코드를 `if page.comments`와 `endif` 사이에 위치하고싶은 곳에 넣으면 된다고 하는데

    ![DISQUS 코드 적용](/assets/posts/jekyll/Add-Comment/Disqus6.png)

    Centrarium은 위 처럼 이미 적용되어 있네요!!!

    이제 코드의 `yourSiteShortName`이 `site.disqus_shortname`이 전역 변수로 적용되어 있으니 `_config.yml`에 가서 변수를 추가해주겠습니다.

    ![DISQUS 코드 적용](/assets/posts/jekyll/Add-Comment/Disqus7.png)

    물론 `yourSiteShortName`에 직접 넣어줘도 됩니다.

## 댓글 기능 완성!!!

![댓글 테스트](/assets/posts/jekyll/Add-Comment/Disqus8.png)

## Guest 댓글 허용

DISQUS 댓글은 Disqus / Facebook / Twitter / Google 으로 로그인 하여 댓글을 달 수 있습니다.

물론 Guest가 댓글을 추가하게 설정할 수도 있습니다.

검색을 해보면 [DISQUS 공식 Q&A 사이트](https://help.disqus.com/en/articles/1717211-guest-commenting)에서 Admin - Setting - Community에서 Guest 댓글을 허용할 수 있다고 나오는데 들어가보면 없네요......

Admin - Setting - Moderation에서 옵션을 확인할 수 있습니다.

![Guest 댓글](/assets/posts/jekyll/Add-Comment/Disqus9.png)

체크하고 저장합시다^^

## Moderation

DISQUS 가 Guest 댓글을 막아놓은 이유는 스팸이나 광고 댓글 때문인것 같습니다.

Setting - Moderation에서 댓글에 링크가 달려있거나 제한된 단어들이 있을 때 관리자 승인 / 삭제 / 스팸 등록 같은 작업을 할 수 있는것 같네요

가장 중요한 `링크 방지`나 `미디어 파일 방지`는 유료라서 슬프네요....

익명의 유저가 처음 댓글을 남겼을 경우 관리자의 Moderation이 필수이고 이후 같은 email에 대해 whitelist를 등록할 수 있는것 같습니다.

일단 Guest 댓글을 허용해놓고 나중에 문제가 생기면 막을 계획입니다.
