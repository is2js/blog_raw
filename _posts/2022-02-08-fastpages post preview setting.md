---
toc: true
layout: post
description: 이미지 표기 등 수정해보기
categories: [fastpages]
title: fastpages post preview setting

image: "images/posts/blog.png"

---

### fast pages 메인에서 preview image 보이게 하기
- 참고: [이미지를 표기하는 fastpages](https://prrao87.github.io/blog/)

1. **`_config.yml`에서 `show_image: false -> true`로 변경**

2. `미리캔버스`를 검색해서 템플릿으로 생성할 이미지 만들기
  - 생성 목록
    - algo.png
    - blog.png
    - config.png
    - data.png
    - default.png
    - git.png
    - java.png
    - python.png
    - sql.png
    - wootech.png

3. **최상단 `images폴더 > posts폴더`에 이미지들 넣어두기**

4. **post마다 `image: "images/posts/blog.png"`형식으로 preview 이미지 설정하기**
  - 메인 위쪽은 `index.html`
  - 메인 아래쪽 포스트 담당은 `home.html`이다
    - _config.yml에서 show.image: true일 경우
      - post_list.html(글만) -> `post_list_image_card.html`로 바뀌어서 보인다.