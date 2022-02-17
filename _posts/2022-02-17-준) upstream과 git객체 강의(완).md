---
toc: true
layout: post
title: git강의(upstream, git객체)
description: 준의 git 기본강의

categories: [git, 우테코]
image: "images/posts/git.png"
---

### 준의 git 강의

#### upstream

![image-20220216104610592](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216104610592.png)



- **기본적**으로 **최초push**에서는
    - local ->  origin의 연결에  `local에 대한 상대적 상류(upstream) `를 `-u`로 명시해줘야한다.
    
    - -`u`는 `--set-upstream`으로 상대적인 상류를 표기한 것이다.
    
- **일반적**으로 
    - **origin에 대한 origin이 `upstream`**이라고 **별칭**한다.
        - 미션repo(`upstream`) -> fork repo(`origin`) -> clone repo(`local`)
    - 최초 명시해준 것처럼 **upstream**은 downstream에 대해 상대적인 것이다.



- 깃허브 초기 메세지 박스를 보면 `-u`가 있는데 **상대적 상-하 관계를 설정하는 upstream설정을 요약**한 단어다

    - 없으면 push가 안된다.
    - 내 local의 remote인 `origin`이라는 원천이라는 upstream으로 사용한다
        - 보통은 원천(origin)보다 위쪽(stream)은 없다.  
        - 하지만, **`fork`로 `가운데origin`이 생기면서, 원본 repo를`별칭stream`을 사용하게 된다.**
        - **우테코의 경우, 미션repo를 fork해해서 `origin`보다 더 상위단계가 생기므로 `upstream`**

    ![image-20220216105030785](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216105030785.png)

    - **여러개의 downstream**이 존재함을 안보이더라도 생각해야한다.
        - **git clone시**((origin <-> local)에는 origin을 알고 있기 때문에 **자동으로 stream관계가 설정**되어 push할 때 `-u`를 통한 upstream 설정은 안해도 된다. 





#### --help명령어와 git 객체

- 퀴즈1
    ![image-20220216105638129](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216105638129.png)

    `O`

    - `git help`메세지들을 잘 보자.
        ![image-20220216105744351](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216105744351.png)

        - `.git`안에 깃 저장소를 초기화 했습니다.
        - `ls -al` 숨긴 파일, 폴더 보면 `.git`이 깃 저장소(`로컬 저장소`)가 보인다.

        ![image-20220216105853695](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216105853695.png)

        - `tree .git/`으로 로컬저장소의 구조를 보면![image-20220216105941160](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216105941160.png)

    - 명령어 도움말은 `명령어` + **`--help`를 뒤에 달면된다.**
        ![image-20220216110218535](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216110218535.png)
    
- 퀴즈2
    ![image-20220216110227506](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216110227506.png)

    - blob은 파일으로 생각하자.

    - 답은 `5`번

        - branch는 commit객체에 대한 참조만 가지고 있다.

        ![image-20220216110335830](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216110335830.png)

    - commit

        - 커밋 = 작업 디렉토리 스냅샷  = 세이브 포인트
        - **돌아가기 위한 포인트.. commit + push만 하지말자**
        - 돌아가기 위한 정보를 어떻게 담고 있을까

        ![image-20220216110639929](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216110639929.png)

    - add시 생성객체, commit시 생성객체를 나누었다.

        ![image-20220216110841734](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216110841734.png)
        ![image-20220216110908919](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216110908919.png)

        - `add`하면, git이 **`a.txt`를 관리대상으로 인식**한다.
            - **아까는 없었던, `index`와 `object`가 추가 된다.**

        ![image-20220216111032274](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216111032274.png)

        - `commit`하면, tree(파일관련), commit(tree를 담고 있는)객체까지 만들어서 2개가 더 만들어진다.

        ![image-20220216111155902](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216111155902.png)

        

        

        

        

    - 퀴즈3

        ![image-20220216111320270](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220216111320270.png)

        - git은 변경사항만 저장한다?
            - 흔한 오해 -> **파일을 통채로 저장한다!**
            - 엄청 뒤로 갈 때, 변경사항만으로 추적? -> **연산 엄청 많아진다. 그냥 속도측면을 중요시해서 `통채로 저장` = `객체`로 관리**
            - **원본 파일마다 `tree`객체를 가지고 있다.** 통채로 저장해놓고, 그것들을 관리하기 위한 커밋이 가진다.
                - 마지막 commit상태의 blob을 본다.
                - 변경사항을 저장하는게 아니라서..


