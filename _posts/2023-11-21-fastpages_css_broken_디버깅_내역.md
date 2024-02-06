---
toc: true
layout: post
title: fastpages 블로그 문제점 디버깅
description: fastpages 블로그 문제점 2가지 디버깅

categories: [fastpages, minima]
image: "images/posts/blog.png"
---
### 문제점1) 갑자기 build가 안된다.
- 찾아보니 대부분 `_config.yml`에 걸려있는 연동 `remote_theme`의 업데이트 문제다
    - 이번 업데이트에서 social_links의 작성법이 바뀌었단다.
- 관련 글: https://stackoverflow.com/questions/74408145/minima-theme-build-shows-liquid-exception-invalid-syntax-for-include-tag-file

#### 해결 방법
- 아래와 같이 `_config.yml` 속 `minima: social_links:`의 작성 방법을 바뀐 것에 맞게 재작성

```yml
# Github and twitter are optional:
minima:
  social_links:
    - { platform: rss, user_url: "/feed.xml" }
    - { platform: github, user_url: "https://github.com/is2js/" }
    - { platform: twitter, user_url: "https://twitter.com/yarotheslav" }
    - { platform: linkedin, user_url: "https://www.linkedin.com/in/yshmarov/" }
# -> https://stackoverflow.com/questions/74408145/minima-theme-build-shows-liquid-exception-invalid-syntax-for-include-tag-file

```
### 문제점2) 어느 순간부터 블로그 css가 깨졌다

- css가 깨졌는데, scss로 컴파일 하는 것 같고, frontend 영역은 잘 모르는 상태

#### 해결 방법
1. `fastpages minima css broken` 검색
2. Fast.ai 포럼 글 중 
    - [Fastpages site not rendering properly](https://forums.fast.ai/t/fastpages-site-not-rendering-properly/104230) 글을 찾음.

    - 대부분 minima의 변동 사항의 문제였던 것 같다.
    - minima의 `layout: default`의 layout 이름을 `layout: base`로 변경했다고 한다.
        - ctrl + shift + F로 `layout: default`를 찾아서 ctrl + shift + H로 `layout: base`로 변경


3. 과정에서 문제가 생겼는지 search 클릭시 https://https:// 2번 붙어서, `search.js` 수정
    ```js
    //search.js
    resultLink.classList.add('search-result');
    // resultLink.setAttribute('href', "https://" + doc.url);
    // -> 2차수정) 디버깅 이후, https://https://로 시작하게 되서 다시 돌리기
    resultLink.setAttribute('href', doc.url);
    ```