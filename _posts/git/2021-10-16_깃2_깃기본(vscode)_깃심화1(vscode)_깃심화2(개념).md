---
toc: true
layout: post
categories: [git]
title: git시뮬2-심화) vscode를 활용해서 git 심화내용 학습하기
description: git의 심화내용을 vscode 활용 2 part, 개념 1 part 나누어 학습
---



- Reference
    - [memi dev](https://www.youtube.com/channel/UCNbp-JsE2BUZMwPaC6J8ApQ) 
    - [얄팍한 코딩사전](https://www.youtube.com/channel/UC2nkWbaJt1KQDi2r2XclzTQ)
    - [kaist 전산학부학생회](https://www.youtube.com/channel/UCO1RNdbf-2x-pzc8ZxHKNZA)



## 깃 기본 with vscode

### extension 2개 설치

1. git extension pack

- `git extension pack` 검색후 설치
- gitlens와 gitignore가 동시에 설치되어진다.
- 왼쪽메뉴에 소스제어 를 클릭하면, gitlens가 통합되서 나온다.
    ![image-20210504205923102](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210504205923102.png)
- 설정 > `git enable`이 활성화 되어있는지 확인한다.



- gitlens layout 수정

    - gitlens를 왼쪽메뉴에 따로 보이게 되었는데, 설명서를 읽으니 `gitlens.setViewsLayout` 검색 > `default`로 클릭해야 **source control탭에서 통합해서 볼 수 있다.**
        ![image-20210505205146803](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210505205146803.png)

    

- _git history는 팔레트에서 git log를 검색해서 사용



2. markdown all in one

`markdown all in one`검색 후 설치





### gitignore (팔레트커맨드)

- **add전**에 설치된 **gitignore**를 **사용**하기 위해서**ctrl+shift+p(모든 명령어, 팔레트)**에서 `gitignore`를 검색하여, **`add ignore`**를 클릭
    - 이후 **`python` 을 검색**해서 선택 -> **파이썬을 제외시키는게 아니라 python관련 불필요 파일들이 등록이 된다.**
    - create `.gitignore`가 생성되었다고 뜬다.

- 소스제어에서, gitignore도 뜨는데, [파일열기]버튼이 따로 있다.
    ![image-20210504213410000](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210504213410000.png)



- `python`프로젝트를 선택하더라도 **`.vscode/`폴더**를 맨끝에 추가해준다.

    - .vscode로 달아도 되지만, 폴더라서 마지막에 슬래쉬도 넣어준다.

    ![image-20210504214645218](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210504214645218.png)



### add remote(팔레트커맨드)

1. 빈repo를 생성

    - **readme파일만 체크해서 생성( 첨부터 push안되게 충돌내기 위함. )**

2. `> add remote` 후 repo 선택

    - **`주의` [remote name]: `origin`으로 기본적으로 설정한다!!!**

    - 참고) `add remote`를 2개 했다면? `git remote` + `remove [remote name]`

3. `git config --list`로 연결된 원격레포의 주소 확인하기





### add, commit, and push in commits

- U 파일들에서, `+`버튼을 눌러 **add**시키면, `staging된 변경사항`으로 이동한다.

- **commit**은 상단의 [소스제어]에서 `체크`버튼으로 **staging된 변경사항을 한번에 다 commit시킨다.**

- **`[commits] `탭** push를 클릭했다.

    - 해당 repo에서 push내역을 확인

    ![image-20211012224716917](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211012224716917.png)







### pull(명령어) 및 이질적인 프로젝트 병합 허용(--allow-unrelated-histories)

```sh
git pull origin main
```

- 현재 remote가 빈 레포가 아니므로, push가 바로 안됨

    - readme.md가 있기 때문에 pull당겨와서 충돌해결하고 해야함.

        ```
        fatal: refusing to merge unrelated histories
        ```

    - 이건 뭔짓을 하더라도 안됨. **따로 설정하면서 pull**을 해줘야한다.



```sh
git pull origin main --allow-unrelated-histories
```





### push

- 갑자기 commits탭에서 사라져서.. 그냥 

    - **`vscode 작업표시줄을 클릭하여 push`**

    - **`F1(팔레트)열고 git push검색해서 push`**

        해도 됨

    ![image-20211012225716315](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211012225716315.png)





### [개념] branch (실무 많이)

1. `main`은 항상 `deploy상태`로 유지
2. `topic branch` = `수정용 브랜치` = push해서 PR하는 브랜치
    - **push를 자주해서 날라가는 것을 방지**하자.
    - **검토후 merged(PR)후에 바로 삭제하는 브랜치**
3. `develop branch`는 삭제안한다.
4. `main`브랜치는 항상 최신으로 유지하므로 `각 기능별 브랜치`는  main에서 딴다.
    <img src="https://raw.githubusercontent.com/is3js/screenshots/main/image-20210505003509762.png" alt="image-20210505003509762" style="zoom: 67%;" />





### [실습] branch(명령어)

![image-20210505003749969](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210505003749969.png)

- **from-br없이 브랜치를** 따면, `현재 브랜치`에서 따는 것이므로 **`git branch`명령어를 확인하고 따자.**
    - git branch `new-br` `from-br`
    - git branch
        - git branch `new-br`



- **`-`로 직전브랜치를 checkout할 수 있다.**

- **`checkout -b` [new-br] [from-br]**로 **생성하면서 동시에 브랜치를 변경**하게 되므로 **가장 자주 쓴다**.

    ```
    git branch testbr main
    git branch
    * main
      testbr
    
    git checkout testbr
    Switched to branch 'testbr'
    
    git branch
      main
    * testbr
    
    git checkout - 
    Switched to branch 'main'
    Your branch is up to date with 'origin/main'.
    
    git branch
    * main
      testbr
      
    git checkout -b work main
    Switched to a new branch 'work'
    
    git branch
      main
      testbr
    * work
    ```

    

### git merge 와 fastforwad

- [그림 출저](https://yuja-kong.tistory.com/entry/Git-branch-%EB%B3%91%ED%95%A9%ED%95%98%EA%B8%B0-merge-fast-forward%EC%99%80-merge-commit%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90)

![image-20211012235425670](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211012235425670.png)
![image-20211012235500471](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211012235500471.png)
![image-20211012235529831](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211012235529831.png)

- **`merge commit`**은 서로 다른 상태의 브랜치를 병합해서 새로운 commit을 만드는 경우이고

    **`fast forward`**는 **동일 내용이 포함되는 브랜치**일 경우 **브랜치 이동만으로 병합해서 따로 commit을 생성하지 않는 경우**입니다.





### topic branch(작업용브랜치)  main에 merge후 삭제: git merge --no-ff / git branch -d

- 현재 브런치 목록

    ```
      main
      testbr
    * work
      remotes/origin/main
    ```

    

1. `work`를 토픽브랜치라 가정하고 파일 `work.py`생성 후 커밋

2. `main`으로 돌아오면 당연히 파일이 없음.

3. `main`에서 merge했다는 커밋을 남기면서(`--no-ff`)  `work`브랜치를 받아오자.

    - 파일의 변화가 차이밖에 없는 경우. HEAD만 이동시키는 merge는 커밋이 발생안하고 앞으로 이동만 ->  `fast  forward`가 발생하는데, 여기서는 직접 merge 메세지도 남긴다.

    ```sh
    ❯ git merge --no-ff work
    ```

4. **merge의 log확인을 `git history`라는 익스텐션으로 해보자.**

    - 나는 git graph라는 익스텐션도 보인다.

        ![image-20211014235222467](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211014235222467.png)

        - git history
            ![image-20211014235337124](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211014235337124.png)

        - git graph

            ![image-20211014235350860](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211014235350860.png)

        - git log --oneline --graph 의 명령어

            ![image-20211014235602708](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211014235602708.png)



5. merge확인후 **topic branch삭제**

    ```sh
    ❯ git branch -d work
    Deleted branch work (was cbf2765).
    ```





### git 탭에서 브랜치 변경후 개별커밋들 확인



![image-20211015000149964](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211015000149964.png)

1. 해당브랜치 화살표누르면 switch (checkout 됨)

2. **왼쪽 화살표를 누르면, 각 브랜치마다 커밋 목록이 보인다.**

    - **브랜치 생성이전 main브랜치 커밋이 그대로 보이는 듯함.**

    ![image-20211015000405590](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211015000405590.png)
    ![image-20211015000648926](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211015000648926.png)

3. `testbr`에서만 파일 생성후 커밋해보자.

    - testbr이 위로 올라왔다. 
        - branch 생성이후부터 달라진 커밋 차이가 난다.
            ![image-20211015001328774](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211015001328774.png)

    



### [중요] topic branch에서   [다른topic이 merge된 main] 의 파일 1개만 받아오고 싶다면: git checkout -p

- `testbr`에서   `main`속 work.py를 가져오고 싶다면

    - **`git checkout -p`** [가져올브랜치명] [가져올파일명]을 이용한다.
        - wsl2에서 안되서 windows 상태에서 하니깐 됬음.

    

    

​	1. `git checkout -p main work` + `y` 로  main브랜치의 work.py를 가지고 온다.

```sh
❯  git checkout -p main work.py

diff --git b/work.py a/work.py
new file mode 100644
index 0000000..e69de29

(1/1) Apply addition to index and worktree [y,n,q,a,d,?]? y
```



2. 현재 상태를 보면 **add까진 된체 로 온다.**
    ![image-20211015004734881](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211015004734881.png)
    - add전후 살펴보는 `git status`로 봐도, 이미 Add는 되어있다.
        ![image-20211015004808921](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211015004808921.png)
        ![image-20211015005024857](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211015005024857.png)



3. work.py에 내용을 조금 넣어주고 commit하자.
    ![image-20211015004904377](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211015004904377.png)





### 직전 commit의 메시지 변경: git commit --amend

1. `git commit --amend`를 치면, 직전 commit에 대한 메세지 수정사항이 editor 로 열린다.

```
git commit --amend
```



2. **메세지를 저장하면 자동으로 반영**되어진다.

3. 참고

    - **마지막 커밋A에 일부만 빠졌다?**
        - 3을 빼고 1,2만 add 및 커밋한 상태
        - **`3을 git add`한 뒤 `git commit --amend`**
    - **그냥 커밋메세지만 변경**
        - `git commit --amend -m "메세지"`

    



### HEAD포함 n번째[에] commit 합치기or삭제 등 commit history 조절: git rebase -i HEAD~[숫자] -> fixup or squash

- **HEAD~** = **HEAD~1**
    - HEAD포함 HEAD1개 전
        - `~1` = `HEAD^` : 헤드의 부모, 헤드의 1개전

- rebase는 명령어로 하는게 더 빠르고 쉽다.

![image-20210505210227153](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210505210227153.png)



- commit 1개당 1개의 의미를 가져야하나, **의미가 없는 커밋들을 하나로 합칠** 때 , `git rebase -i HEAD~` + `[합칠 커밋갯수]` 이후에
    - **`fixup`: (귀찮아서) `HEAD포함` n번째커밋`에` 커밋합치면서 `n번째 커밋명`으로  커밋합침**
    - **`squash`: (새로운커밋명을 위해) `HEAD포함` n번째커밋`에` 커밋합치면서 + `새로운 커밋명`으로 합침**
    - **그외 reword(= amend), drop 등: 사용안한다.**



1. 커밋을 1111,222,3333333,4444,55555 5개를 새로 만들자
    ![image-20211017013823583](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211017013823583.png)

2. **`git rebase -i HEAD~[head포함 합칠 커밋갯수 & head에서 뒤로 2칸 간 곳에서 합쳐짐.]`**을 커맨드로 입력한다.

    ```sh
    ❯ git rebase -i HEAD~2
    ```

    ![image-20211017023334598](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211017023334598.png)

    - 여기서 커밋메시지 같은 `git-rebase-todo` 메세지에서 **`pick`대신 `fixup`을 적어서 뭉개자.**
        - 최근커밋인 5555(HEAD)를 fixup으로 뭉개서 저장시키면 된다.
            ![image-20211017023433229](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211017023433229.png)
            ![image-20211017023443757](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211017023443757.png)

    **따로 뭔가 뜨지 않으면 `.git > rebase-merge > git-rebase-todo`를 클릭한다.**
    ![image-20211017020441390](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211017020441390.png)

    

3. 도착지(HEAD포함 n번째 커밋)는 선택지가 없다.

    - 최근 것이며 뭉개지는 HEAD는 선택지가 있다.

        - 여기서 `fixup`을 선택하면, **귀찮게 새로운 커밋메세지를 작성안하고 n번째 커밋명을 이용하게 된다.**

            ![image-20211017021919308](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211017021919308.png)
            ![image-20211017022107109](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211017022107109.png)

        - `start rebase`를 해서 커밋을 합친다.





5. 이제 444 ->333으로

    ```sh
    git rebase -i
    ```

    ![image-20211017023813902](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211017023813902.png)

    최근 것(아래 것)을 **pick to `squash`로 변경해보자.**

    - **과거커밋으로 합**치는데 **커밋메세지는 새로작성한다는 것이다.**

        ![image-20211017023856342](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211017023856342.png)

    - 맨 위에 커밋메세지를 적으면 적용된다.
        ![image-20211017023934918](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211017023934918.png)
        ![image-20211017023943718](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211017023943718.png)





### 이미해버린 commit 취소는 직전HEAD로 돌아가면서 취소: git reset --hard HEAD~

- reset은 **해당커밋**까지가면서 자연스럽게 삭제한다.

1. head의 부모로 가면서, head는 삭제
    ![image-20211018014043041](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211018014043041.png)

    ```sh
    ❯ git reset --hard HEAD~HEAD is now at 3460720 Merge branch 'testbr' of https://github.com/is2js/git-ssac into testbr
    ```

    ![image-20211018014057536](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211018014057536.png)





### add(staging) 취소는 아직commit안됬으므로 commit전 HEAD로 돌아가면서 취소: `-` or git reset HEAD

- git extension으로 는 그냥 `-`버튼을 누르면 add가 취소됨
    ![image-20210506233547359](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210506233547359.png)





- 명령어로는 **`git reset HEAD`**로서 

    - **`--hard`없이 code를 살린체** 최근HEAD로 돌아가면 -> `add취소가 자동`으로

    ![image-20211023000434343](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211023000434343.png)

    ```sh
    git reset HEAD # HEAD 대문자여야함.
    ```

    ![image-20211023000445814](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211023000445814.png)





## 깃 심화1 with vscode

### 

- **`git tag`는 `release`의 의미를 가지므로 `main`에서  딴다.**
    - tag
        - 혼자 = 커밋의 합 = 강의 n화
        - 협업 = **topic -> main에서 merge후에 main에서 `배포버전`으로서 tag를 딴다.**





### merge는 항상 명령어로 --no-ff를 붙여 커밋을 남기자

- `--no-ff`를 붙이면 fastfoward = 커밋없이 head위치만 바꾸는 것을 안하고 **항상 merge커밋을 남긴다.**
    - **conflict나면, Accept incoming후  `git commit`만 입력해주면 메세지 완성되어있음.**



1. `main`브랜치로 넘어가기

2. 명령어로 merge하기

    ```
    ❯ git merge --no-ff testbr
    Merge made by the 'recursive' strategy.
     hello.py  | 3 +++
     testbr.py | 0
     work.py   | 1 +
     3 files changed, 4 insertions(+)
     create mode 100644 testbr.py
    ```

    

### merge후 topic branch는 삭제: git branch -d

3. **merge가 되었다면, topic 브랜치 삭제하기!!**

    ```
    git branch -d testbr
    
    Deleted branch testbr (was 74296bb).
    ```

    - **만약, fully merge가 안되었다고 뜨면? 대문자 `-D`**로 강제 삭제해준다.

        ```
        git branch -D testbr
        ```

        



### tag 확인 및 생성 in main브랜치:git tag [v0.0.1]

- tag는 **release될 `main`브랜치**에서 작성한다.
- tag는 **release단위 = 배포단위로 생성**한다.
- tag는 **소스코드를 push를 해놓은 상태에서 -> tag를 local에서 따고 ->tag를 push한다.**



```sh
❯ git tag

❯ git tag v0.0.1

❯ git tag

v0.0.1
(END)
```





### tag는 commit과 별개로 따로 push: git push origin [tag(=버전)]

1. `git push origin [태그명=버전명]`

    ```sh
    ❯ git push origin v0.0.1
    Total 0 (delta 0), reused 0 (delta 0)
    To https://github.com/is2js/git-ssac.git
     * [new tag]         v0.0.1 -> v0.0.1
    ```

    

2. git repo에, tag가 생긴 것을 우측사이드에서 확인가능
    ![image-20211019003006735](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211019003006735.png)





### code수정 후 commit->push 끝난 뒤, 따로 tag 따고 -> push

1. 파일(코드) 수정

2. local git **commit**

3. local -> origin(**원격레포**)로 **push**

    ```sh
    ❯ git add -A
    ❯ git commit -m "askldjflkasd"
    ❯ git push
    ```

    

4. **commit의 push가 끝난 뒤, `tag`를 딴다.**

    ```sh
    ❯ git tag v0.0.2
    ❯ git push origin v0.0.2
    Total 0 (delta 0), reused 0 (delta 0)
    To https://github.com/is2js/git-ssac.git
     * [new tag]         v0.0.2 -> v0.0.2
    ```

    

### tag는 과감하게 돌아가기: git reset --hard [태그]

- `git reset --hard [commit_hash]`를 하면 **해당 커밋까지 돌아가면서 자연스럽게 그 이후의 내용들은 삭제되었다.**
    - **하지만, 태그의 경우, `code + commit`은 돌아가나 `tag`는 삭제안되고 남아있다.**



```sh
❯ git reset --hard v0.0.1
HEAD is now at bd4c018 Merge branch 'testbr' into main


❯ git tag

v0.0.1
v0.0.2
```

- **되돌아가도 tag는 남아있으니 직접 삭제해줘야한다.**



### reset tag이후에도 local+remote tag 따로 삭제: git tag -d 태그 / git push origin -d 태그

1. **로컬 tag를 삭제**한다.

    ```
    git tag -d v0.0.2
    
    Deleted tag 'v0.0.2' (was 476f70b)
    ```

2. **원격에 남아있는 tag도 삭제**한다.

    ```
    git push origin -d v0.0.2
    
    To https://github.com/is3js/py.git
     - [deleted]         v0.0.2
    ```







## 깃 심화2 with 개념위주



###  실수로 다른브랜치에 커밋or코드의존성때문에 다른사람 커밋이 필요할 때: git cherry-pick 으로 특정커밋만 똑! 가져올 때

![image-20211011002343727](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011002343727.png)

- 코드의존성

    - 다른사람 커밋 위에서 일해야하는 경우
        - ex> 다른사람이 버그고침 -> 나는 그위에서 일을 해야할 때

- master branch의 cccccccc커밋만  -> my branch로

    - 커밋해쉬 앞 7글자

        ```
        git cherry-pick ccccccc
        ```

        ![image-20211011002712243](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011002712243.png)





### git reset 총정리(add취소, commit취소)

- `ADD취소` 2가지

    - **옵션을 안주면 `mixed`**

    ![image-20211011003126389](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011003126389.png)

- `커밋취소` 3가지

    - `HEAD`뒤에 눈웃음 한개 `^`
        - `--soft` : commit만 취소 + `add + 내용물 그대로`
        - **`(mixed)` : commt취소 + add취소 + `내용물만 그대로(unstaged)`**
        - `--hard` :  commit취소 + add취소 + 내용물취소

    ![image-20211011003219453](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011003219453.png)



### HEAD와 ^, ~숫자

- `HEAD`: **`로컬` workingDir의 `현재 작업중인 커밋`**
    - switch하면 작업폴더, 작업하는곳도 바뀌니 head도 바뀐다.
    - **add만 했다면? 커밋이 아직 아니므로 HEAD가 보고 있진 않다.**
- **HEAD`^`: 현재작업중 커밋의 `부모커밋`**
    - `^^` : 부모의부모
    - **`~3` : `^^^`쓰지말고, 3단계 전 커밋**
        ![image-20211011004343131](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011004343131.png)
        ![image-20211011004351053](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011004351053.png)
        ![image-20211011004403666](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011004403666.png)
        ![image-20211011004429954](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011004429954.png)





### 다시git reset HEAD 살펴보기with 돌아가서 자연스럽게 취소

1. add 취소

    - 커밋이 아니므로, HEAD는 아직 안보고 있다.

    - 그래서 git reset HEAD를 하게 되면, **HEAD커밋을 삭제하는게 아니라 `git reset [여기]` = `[여기]로 돌아가겠다` = 해당커밋이후로 add는 커밋은 필요없다. `자연스럽게 add를 취소`**
        ![image-20211011004613267](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011004613267.png)

        ![image-20211011004726483](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011004726483.png)
        ![image-20211011004929115](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011004929115.png)





2. 커밋취소![image-20211011005120724](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011005120724.png)

    - `git reset HEAD^` 현재의 부모커밋으로 가고 싶다.

    - **를 통해서 현재커밋을 취소**

        ![image-20211011005133219](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011005133219.png)



### 작업중에 다른작업요청시 commit도 reset도 애매한 임시저장: git stash

- 로그인 **브랜치에서  작업중인데, 갑자기 로그아웃 브랜치를 수정해달라고함.**

    - 브랜치를 정리해야지만 브랜치변경이 가능함.
        - 커밋한다음 reset을 해도되긴하겠지만,,,
    - **커밋하기엔 미완성, 작업물을 날릴순 없다. 임시저장으로 정리**

- 현재 어느정도는 add된 상태

    - unstage된 = add되지 않는 파일도 있음.

        ![image-20211011005844708](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011005844708.png)
        ![image-20211011005931612](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011005931612.png)

    - **add, unstage 다 합쳐서 사진을 스태시! 하고 찍는다.**

        ```
        git stash
        ```

        ![image-20211011010019725](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011010019725.png)

        - **커밋이후의 상황들 전체가 `stash 바구니`에 담기게 됨.**

            ![image-20211011010055227](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011010055227.png)

    - **바구니에 담기면 사라진다.**
        ![image-20211011010208956](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011010208956.png)

- **`git stash pop`을 통해 stash바구니에서 꺼내면 다시 적용된다.**

    ![image-20211011010245179](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011010245179.png)

    - stack처럼 pop으로 나온다.
        ![image-20211011010324195](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011010324195.png)
        ![image-20211011010402997](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011010402997.png)

- git stash, git stash pop 이외에 `git stash list`도 있다.
    ![image-20211011010431876](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011010431876.png)



### 마지막 커밋 변경(내용추가, 메세지변경): git commit --amend

![image-20211011010616653](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011010616653.png)



- **마지막 커밋A에 일부만 빠졌다?**
    - 3을 빼고 1,2만 add 및 커밋한 상태
    - **`3을 git add`한 뒤 `git commit --amend`**
- **그냥 커밋메세지만 변경**
    - `git commit --amend -m "메세지"`











### merge말고 1줄로 합치는데, 상대주는놈branch 위에 올려서 합칠 때:git rebase <branch명>

- 내 브랜치들을 **`주는놈branch` 위에서 적용시키고 싶을 때**

    - 최근브랜치가 내꺼가 되면서, 내가  주는놈 위에 적용됨.

    ![image-20211011011157120](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011011157120.png)

    ![image-20211011011211637](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011011211637.png)

- **주의할점으로, 주는놈위에 쌓아올려질 때, 그대로 가는게 아니라 `커밋해쉬가 달라짐`**
    ![image-20211011011300333](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011011300333.png)



### merge vs rebase

![image-20211011011440989](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011011440989.png)

- merge와 비교
    - **장점: 커밋해쉬 유지 및 흐름 유지되어 돌아갈 수 있음**
    - 단점: **merge라는 불필요커밋을 생성함.**
- rebase
    - 장점: **주는놈위에 받는놈위주로 정리가 됨. 깨끗한 커밋 히스토리가 됨.**



### 커밋 합침, 삭제 등 커밋 히스토리 조절: git rebase -i 

![image-20211011011620097](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011011620097.png)





### pull받은이후 rebase로 커밋해쉬가 바뀌었다면? 어쩔수 없이 git push --force

![image-20211011013551223](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011013551223.png)

- remote branch2개를 잘 받아왔다.

- 이후 **`git rebase(주는놈위에 쌓아서 합침)`로 내 커밋해쉬가 바뀌었다면?**

    ![image-20211011013641088](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011013641088.png)

    - **이 상태에서 git push하면 reject**된다.
        ![image-20211011014624104](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011014624104.png)



- 어쩔수 없이 **`git push --force`로 덮어씌워야함.**

- 협업중엔 조심해야한다.

    - **남자가 rebase후 푸쉬포스**로 덮어씌워진다면,,

        ![image-20211011015657705](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011015657705.png)
        ![image-20211011015709288](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011015709288.png)

    - **같은 브랜치 협업중**에는 미리 말안하는 이상 rebase 및 push force를 하면 안될듯



### alias

- git push까지

    - status diff add cache? commit log

- git폴더에

    ![image-20211011020355626](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011020355626.png)

- 내 환경설정에
    ![image-20211011020850041](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211011020850041.png)

- 참고

    - [히데쿠마 설정](https://github.com/hidekuma/dotfiles/blob/master/bashrc.cafe24.wsl)

        ```sh
        ## alias
        
        # git
        alias gst='git status'
        alias gc='git commit'
        alias gm='git merge'
        alias gr='git reset'
        alias ga='git add'
        alias gaa='git add --all'
        alias gloga='git log --oneline --decorate --color --graph --all'
        alias glog='git log --oneline --decorate --color --graph'
        alias gco='git checkout'
        alias gb='git branch'
        alias gcb='git branch | fzf -m | xargs git checkout'
        alias gmb='git branch | fzf -m | xargs git merge'
        alias gdb='git branch | fzf -m | xargs git branch -D'
        alias gp='git push'
        alias gf='git fetch'
        alias gl='git pull'
        alias gd='git diff'
        alias ghf='git ls-files -v | grep "^h" | cut -c3-'
        
        
        # dkr
        alias dkr='docker'
        alias dkrp='docker-compose'
        # for docker
        #sudo mount --bind /mnt/c /c
        sudo mount --bind /mnt/d /d
        export DOCKER_HOST=tcp://0.0.0.0:2375
        
        # basic
        alias dotfiles='cd ~/dotfiles'
        alias ll='ls -al'
        alias l='clear && ls -al'
        #alias cdd='cd /d/'
        alias ws='cd /d/workspace'
        #alias solup='cd /d/workspace/solup'
        alias echost='cd /d/workspace/solup/echost'
        alias 12r='cd /d/workspace/solup/12r'
        alias private='cd /d/workspace/private'
        alias ..='cd ..'
        alias ..2='.. && ..'
        alias ..3='..2 && ..'
        
        # some more ls aliases
        #alias ll='ls -alF'
        alias la='ls -A'
        #alias l='ls -CF'
        
        # Add an "alert" alias for long running commands.  Use like so:
        #   sleep 10; alert
        alias alert='notify-send --urgency=low -i "$([ $? = 0 ] && echo terminal || echo error)" "$(history|tail -n1|sed -e '\''s/^\s*[0-9]\+\s*//;s/[;&|]\s*alert$//'\'')"'
        
        
        # for python
        alias python='python3'
        alias pip='pip3'
        export WORKON_HOME=~/.virtualenvs
        export VIRTUALENVWRAPPER_PYTHON='/usr/bin/python3'  # Usage of python3
        source ~/.local/bin/virtualenvwrapper.sh
        # for pyenv
        export PYENV_ROOT="$HOME/.pyenv"
        export PATH="$PYENV_ROOT/bin:$PATH"
        export PYTHON_CONFIGURE_OPTS="--enable-shared"
        export PYENV_VIRTUALENV_DISABLE_PROMPT=1
        
        if command -v pyenv 1>/dev/null 2>&1; then
          eval "$(pyenv init -)"
          eval "$(pyenv virtualenv-init -)"
        fi
        ```

