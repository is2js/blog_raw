---
toc: true
layout: post
categories: [git, github, comment, pr, codereview]
title: github codereview PR comment
description: commit, comment(with emptyline) link, `#`PR번호, `@`사용자

image: "images/posts/git.png"
---

### commit link 복붙

1. **현재 PR의 커밋을 복붙한 경우: `commit번호`으로 표기된다.**
    - 링크: `https://github.com/woowacourse/java-racingcar/pull/330/commits/a1815b89cf878c8e92a27f274e0f857605aa40ff`
    - 표시
        ![20220527090842](https://raw.githubusercontent.com/is2js/screenshots/main/20220527090842.png)
    - 직접 commit번호를 입력하면 링크가 안된다.

2. **다른 PR의 커밋을 복붙한 경우: upstream레포@`commit번호`으로 표기된다.**
    - 링크: `https://github.com/woowacourse/atdd-subway-path/commit/89e7df5a653dc1109b2c18f261a9ee7f81582708`
    - 표시
        ![20220527090933](https://raw.githubusercontent.com/is2js/screenshots/main/20220527090933.png)


### comment link 복붙(위 empty line유무)

1. **현재 PR의 comment를 복붙한 경우: `commit번호`으로 표기된다.**
    1. **comment link위에 글이 있는 경우(`PR 이쁘게` + `(comment)` text 나옴)**
        ![20220527091601](https://raw.githubusercontent.com/is2js/screenshots/main/20220527091601.png)
        ![20220527091706](https://raw.githubusercontent.com/is2js/screenshots/main/20220527091706.png)
    2. **comment link위에 글이 없는 경우(`#PR번호 comment` text만 나옴)**
        ![20220527091541](https://raw.githubusercontent.com/is2js/screenshots/main/20220527091541.png)
        ![20220527091806](https://raw.githubusercontent.com/is2js/screenshots/main/20220527091806.png)

2. **다른 PR의 커밋을 복붙한 경우: upstream레포@`commit번호`으로 표기된다.**
    - comment link위에 emptyline 유무는 똑같이 반영되지만
    - emptyline을 줘서 text만 표기 될 때, 앞에 upstream레포url이 붙게 된다.
    ![20220527092007](https://raw.githubusercontent.com/is2js/screenshots/main/20220527092007.png)
    ![20220527092015](https://raw.githubusercontent.com/is2js/screenshots/main/20220527092015.png)


### `#`은 upstreamREPO 속 PR들 탐색 / `@`는 사용자 탐색
    1. `#` upstream `PR들 탐색`
        ![20220527092151](https://raw.githubusercontent.com/is2js/screenshots/main/20220527092151.png)

    2. `@` `사용자 탐색`
        ![20220527092117](https://raw.githubusercontent.com/is2js/screenshots/main/20220527092117.png)