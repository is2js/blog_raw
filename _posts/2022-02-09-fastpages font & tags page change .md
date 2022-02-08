---
toc: true
layout: post
description: 폰트와 tags 페에지를 수정해보기
categories: [fastpages]
title: fastpages font&tags page change

image: "images/posts/blog.png"

---

### font 바꾸기

- 참고: [한국 블로그](https://anarinsk.github.io/lostineconomics-v2-1/coding-tool/web-tool/2020/03/07/blogging-with-fastpages.html)


1. `_includes`폴더 > `head.html`파일에서 **웹 폰트 link태그 추가해주기**
    ```html
  <!-- 폰트1:  기본 spoqa 웹폰트 -->
  <!-- css에서 * {} 다 뒤집어씀. -->
  <link href='//spoqa.github.io/spoqa-han-sans/css/SpoqaHanSansNeo.css' rel='stylesheet' type='text/css'>
  <!-- 폰트2:  블로그 제목들 sunflower 웹폰트 -->
  <!-- 쥬피터 등 포스트 내부 글자 h1, h2 제목은 sunflower체 도입 -->
  <link href='https://fonts.googleapis.com/css2?family=Sunflower:wght@700&display=swap' rel="stylesheet'>
    ```

2. 해당 폰트를 사용하기 위해 `asseets`