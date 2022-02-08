---
toc: true
layout: post
categories: [git]
title: 협업1) 나혼자 2컴으로 협업(원격작업)하는 방법
description: local to remote -> remote to com1, com2를 시뮬레이션함.

image: "images/posts/git.png"
---

## 나혼자 투컴협업(local to remote to local2)

- Reference
    - [memi dev](https://www.youtube.com/channel/UCNbp-JsE2BUZMwPaC6J8ApQ) 
    - [얄팍한 코딩사전](https://www.youtube.com/channel/UC2nkWbaJt1KQDi2r2XclzTQ)
    - [kaist 전산학부학생회](https://www.youtube.com/channel/UCO1RNdbf-2x-pzc8ZxHKNZA)



### local to remote

#### 이미 로컬에서 관리하던 프로젝트를 github로



1. **local에서 작업시작**

    - 폴더 생성: 

        - `/home/is2js/workspace/git-github`

        - git init

        - 계정입력(이미 global로 했으면 안해도됨)

        - 파일 생성

            - cat
            - mouse

        - 커밋

            ```
            ❯ git add -A
            ❯ git commit -m "FIRST COMMIT"
            ```

    - **혹시나 연결된 원격레포가 있는지 확인**

        - `git remote` 아무것도 안뜨면 없는 것

2. 깃헙 레포 생성(`git-github`) for **로컬을** 원격으로

    - **readmd 등 생성하지 말아야함!**
        - 이미 local에서 git으로 관리하고 있던 프로젝트를 올리는 것이므로 **아무것도 없어야함.**
        - 아무것도 코드가 없을 때 나타나는 화면이 나와야함
            ![image-20211004002803625](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004002803625.png)
        - 구성 : https://github.com/is2js/git-github.git
            - githuburl + `/유저명/레포명.git`

3. `git status`로 현재까지의 상황이 다 커밋되어있는지 확인한다.

    ```sh
    ❯ git status
    On branch master
    nothing to commit, working tree clean
    ```

    - 빠진게 있으면 전부 add -A해서 commit



#### 로컬내용을 빈 원격레포에: git remote 등록 git push 내용옮기기

1. 원격레포에 코드가 없을 경우 뜨는 페이지에서 **`…or push an existing repository from the command line`** 란을 복사버튼을 눌러서 

    - **`현존하는 로컬 깃관리 레포`를, 원격레포에 push하도록 `복사한 내용을 cli에 붙혀넣기` 해준다.**

        - **현재는 코드가 main브랜치를 만들고, main 브랜치로 push하도록 변경되어있다.**

        - 복붙후 내 로컬도 main브랜치로 바껴있다.
            ![image-20211004005039526](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004005039526.png)

        - 아예 master브랜치 -> main브랜치로 **브랜치이름변경**하는 명령어 인가보다.
            ![image-20211004005114221](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004005114221.png)

            ```
            git branch -M main
            ```

            - With a `-m` or `-M` option, <oldbranch> will be renamed to <newbranch>

            ```sh
            > git branch
            
            * main
            (END)
            ```

            

        ```sh
        > git remote add origin https://github.com/is2js/git-github.git
        git branch -M main
        git push -u origin main
        
        Counting objects: 4, done.
        Delta compression using up to 12 threads.
        Compressing objects: 100% (2/2), done.
        Writing objects: 100% (4/4), 273 bytes | 273.00 KiB/s, done.
        Total 4 (delta 0), reused 0 (delta 0)
        To https://github.com/is2js/git-github.git
         * [new branch]      main -> main
        Branch 'main' set up to track remote branch 'main' from 'origin'.
        ```

    - 이 때, 명령어를 뜯어보면

        - `git remote add origin https://github.com/is2js/git-github.git`

            - `git remote add [원격레포의 별칭, default origin] [레포 url]`로서 **원격레포url의 원격레포를 별칭(`origin`)으로 등록**

        - `git branch -M main`

            - **브랜치명**을 main으로 **변경**

        - `git push -u origin main`

            - `git push -u [등록된 원격레포별칭, default origin] [올릴 원격레포의 branch]`

        - 이후 나오는 메세지 중 **현재 main브랜치가 , 원격레포인 origin의 원격 브랜치main을 추적한다.**고 메세지가 뜬다.

            ```
            Branch 'main' set up to track remote branch 'main' from 'origin'.
            ```

    - `git remote`명령어로 **실제로 연결이 잘되었는지 확인**

        ```sh
        ❯ git remote
        origin
        ```

        

2. 빈 원격레포에, 관리되던 로컬 프로젝트 내용이 올라갔다면, 레포의 기본페이지가 바뀐다.
    ![image-20211004005923698](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004005923698.png)

    - 커밋내용은 **로컬에서 커밋한 기록들이 다 뜬다.**





##### 소스트리에서 해보기

1. 소스트리로 해당 레포 열기
2. 저장소탭 > 원격 설정 클릭
    - 현재 나는 이미 터미널로 연결된 상태라, 원격 저정소 경로에 하나가 추가 되어있다.
        ![image-20211004010554478](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004010554478.png)
3. 추가 클릭
    -  원격 이름: 원격url의 원격레포에 대한 별칭, default `origin`
    -  URL/경로: 원격레포의 url 복붙
    -  호스트 종류: github
    -  사용자명: github id 입력
        ![image-20211004010738342](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004010738342.png)

4. **원격 탭에서 원격레포의 별칭(주로 origin)으로 확인하기**
    ![image-20211004010814725](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004010814725.png)



5. **push버튼 클릭**
    - 로컬 / 원격(리모트) 브랜치를 각각 선택해주고 push해주자.
        ![image-20211004010919501](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004010919501.png)







#### 파일추가(수정) 후 commit까지 한 뒤 push: git push [원격레포별칭] [그 레포의 브랜치]

1. dog파일 추가

2. git add - A, git commit -m , **git push [원격레포별칭, 주로origin] [그 레포의 브랜치]**

    ```sh
    ❯ git add -A
    ❯ git commit -m "ADD dog"
    [main 15a73f6] ADD dog
     1 file changed, 1 insertion(+)
     create mode 100644 dog
    ❯ git push origin main
    
    Counting objects: 3, done.
    Delta compression using up to 12 threads.
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 302 bytes | 302.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0)
    To https://github.com/is2js/git-github.git
       d33f3a8..15a73f6  main -> main
    ```

    





#### 올릴필요가 없거나(코드로 자동생성) 올려선 안되는 파일(DB계정 등)처리: .gitignore

- **올릴 필요가 없는 파일 : 코드로 자동 빌드되는 파일들**
    - 그 때 그 때 실행해서 만들면 되므로 올릴 필요가 없다.
        - 안드로이드나 스프링 등에서 **처음부터 골격이 세팅된 체 시작하는 프로젝트들을 생성하면 `.gitignore`가 미리 만들어져있다!!**
- .gitignore에 등록하면
    - add + commit의 대상으로 안봄. 버전관리 안됨.





1. 프로젝트폴더 **최상위 공간안에** `.gitignore`파일 생성

    - 숨김파일로 지정되니 참고해야함

2. gitignore에 넣을 파일 `secret-animal`을 만들고, `git status`로 상황을 확인하자.

    - **`Untracked files(add해야할 목록)`**로서 아직 add안된 파일 2개를 알려준다.

        ```
        Untracked files:
          (use "git add <file>..." to include in what will be committed)
        
                .gitignore
                secret-animal
        ```

3. 이 상태에서 **secret-animal 파일을 .gitignore에 적으면,  `git status`상 `add해야하는 파일에서 제외`된다.**

    - 아하~ 아예 add할 파일로조차 보지도 않음.
    - **내 프로젝트에  있어도 add + commit을 안하므로 push도 관리도 안됨.**

    ```
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
    
            .gitignore
    ```

    







### remote to com1,2



#### 원격repo를 현재폴더에다가 가져오기: git clone [url] .

- 1개 컴으로 연습하려면, 폴더 2개를 만들어서 해보면 된다.

    ![image-20211004230434356](C:\Users\cho_desktop\AppData\Roaming\Typora\typora-user-images\image-20211004230434356.png)

    - 일단 폴더 2개를 만든다
        - `computer-1`
        - `computer-2`
            ![image-20211004230535305](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004230535305.png)
    - 참고) clone시
        - 해당목적지 폴더로이동 > `git clone [url] [.]`
        - 소스트리는 해당 목적지폴더 `선택만`





1. `컴퓨터1`의 폴더를 `vscode`로 연 다음, 터미널도 켠다.

    - ~~윈도우탐색기 > `wt -d .` > `code .` > `ctrl + ~`~~ + `wsl로 repoen?`
        - 윈도우 통해서 들어가면.. `vscode wsl원격버전`이 안켜진다.
    - wt > wsl > 해당폴더로 이동 > `code .`

    

2. **기존 작업하던 원격레포(`git-github`)의 url를 복사한 뒤, vscode 폴더 터미널에다가  `git clone`한다.**

    ```sh
    #git clone [url] [.]
    git clone https://github.com/is2js/git-github.git .
    ```

    - **만약, clone시 [.] 현재폴더 옵션을 안주면, 그 내부에 폴더를 새롭게 만들어버림**

        - 새로운 컴퓨터의 로컬폴더 `컴퓨터-1`에다가 원격repo`git-github`의 내용을 가져왔다.
            ![image-20211004231109553](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004231109553.png)

        



3. **연결된 `나의 원격repo`가 아니라 `아무 원격repo`를 클론해오더라도 `git log`로 모든 작업내역을 볼 수 있다.**

    ```
    git log
    ```





4. **이제 소스트리로 `컴퓨터-2` 로컬폴더로 clone해보자.**

    - `+ (new) tab` > `Clone` > 

        - 소스경로/URL : 원격레포url
        - **목적지 경로: `computer-2`폴더 지정**  
            - **git clone 시에는 [원격레포url] `[.]` 현재폴더로의 옵션을 붙혀줘야하지만, 소스트리에서는 안해주고 폴더만 선택해주면, 그 폴더로 다 clone**

        ![image-20211004231921033](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004231921033.png)
        ![image-20211004232343344](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004232343344.png)

    - 이제 `컴퓨터-2`도 vscode로 열자.



#### 새로올린(뒤쳐진) 커밋 내역 자동 확인: git fetch + git status

- `컴1`작업 -> `원격레포`에 push -> **`컴-2`에서 일일히 작업내역을 확인하지 않고, `git fetch`로 뒤쳐진 내역이 있는지 확인**
    - 컴퓨터1
        ![image-20211004234457087](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004234457087.png)
    - 컴퓨터2
        ![image-20211004234721445](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004234721445.png)



1. `컴1`에서 dog name을 수정하고, add, commit호 `원격레포`에 push까지 해보자.

    - **local의 global계정이 내 계정이라서 그런가 바로 push된다.**

        ```
        ❯ git add -A
        ❯ git commit -m "EDIT dog"
        [main 1eaa722] EDIT dog
         1 file changed, 1 insertion(+), 1 deletion(-)
        ❯ git push origin main
        ```

        ![image-20211004234252470](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004234252470.png)





2. **이제 `컴2`에서 일일히 github에 새로 올라운 커밋내역을 확인하기 힘드니 `git fetch`로 뒤쳐진==새로올라온 커밋의 갯수를 확인한다.**

    ```sh
    ❯ git fetch
    ❯ git status
    On branch main
    Your branch is behind 'origin/main' by 1 commit, and can be fast-forwarded.
      (use "git pull" to update your local branch)
    
    nothing to commit, working tree clean
    ```

    - 원격레포/main 브랜치에비해, 내 브랜치가 1개 커밋 뒤쳐져있다고 나온다.



#### 뒤쳐진 내용 받아오기: git pull [원격별칭] [브랜치명]

3. `컴2`에서 뒤쳐진 1개의 커밋을 받아온다.

    ```sh
    > git pull origin main
    From https://github.com/is2js/git-github
     * branch            main       -> FETCH_HEAD
    Updating 15a73f6..1eaa722
    Fast-forward
     dog | 2 +-
     1 file changed, 1 insertion(+), 1 deletion(-
    ```

    ![image-20211004235244942](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211004235244942.png)





4. **소스트리에서**도 git pull을 하기위해

    - `컴2`에서 dog 이름 수정 

    - **소스트리 `컴2`에서 `커밋과 동시에 push`**

        ![image-20211005011652414](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005011652414.png)

    - **소스트리에서 `컴1` 폴더를 add**

    - `컴1`폴더에서 **`패치버튼 `선택** > `모든 원격저장소에서 가져오기` 선택
        ![image-20211005011927168](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005011927168.png)

        ![image-20211005012036053](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005012036053.png)

    - **main 브랜치에 당겨올 것들이 있다고 메세지가 뜸**
        ![image-20211005012055782](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005012055782.png)

    - **`pull`버튼으로 받아오기**
        ![image-20211005012136613](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005012136613.png)





#### 일단 작업전에 메세지 안떠도 pull부터 누르고 시작하자



## 나혼자 투컴협업 branch, conflict



### 브랜치를 이용해서 투컴협업



#### 브랜치 전환, 없으면 생성해서 : git checkout -b [브랜치명]

- branch기본
    - 생성 : `git branch [이름]`
    - 전환 : `git checkout [이름]`
- **브랜치 생성과 동시에 전환**
    - **`git checkout -b [이름]`**
        - checkout은 커밋id, 브랜치, tag 다되므로.. `-b`옵션을 주면 **브랜치전환인데, 없으면 생성까지**





1. `컴1`에서 `my-idea` 브랜치 생성

    ```sh
    git checkout -b my-idea
    ```

    ![image-20211005013632261](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005013632261.png)

    ```sh
    git branch
    ```

    

2. `my-idea`이라는 평행우주에서 **파일을 수정하고** add commit까지 한 뒤 push할 준비를 해자.

    - mouse 삭제

    - dinosaur 추가

        ```sh
        ❯ git add -A
        ❯ git commit -m "ADD dino"
        [my-idea e43a292] ADD dino
         2 files changed, 1 insertion(+), 1 deletion(-)
         create mode 100644 dinosaur
         delete mode 100644 mouse
        ```





#### 새로운 브랜치(평행우주)를 push: git push [origin] [새로운 origin의 브랜치명]

- `my-idea` 브랜치를 -> origin의 `특정브랜치`로 **생성해서 push할 수 있다.**
    - 하지만 보통은 `원격레포에 로컬과 같은 브랜치명으로 생성`해서 올린다.



3. **`컴1`의 `my-idea`브랜치와 동일한 이름으로 `원격레포의 브랜치를 생성해서 push`까지 해보자.**

    ```sh
    ❯ git push origin my-idea
    
    Counting objects: 3, done.
    Delta compression using up to 12 threads.
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 304 bytes | 304.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0)
    remote: 
    remote: Create a pull request for 'my-idea' on GitHub by visiting:
    remote:      https://github.com/is2js/git-github/pull/new/my-idea
    remote: 
    To https://github.com/is2js/git-github.git
     * [new branch]      my-idea -> my-idea
    ```

    - **new branch로서 [로컬my-idea] -> [원격my-idea]으로 push했다고 나온다. **
        - 즉, 로컬의 평행우주를 push한다고 해서 1:1대응 되는 것이 아니라 **원격의 원하는 이름의 평행우주를 만들어서 push하는 구나.**





4. 원격레포에서 **새로운 브랜치(평행우주)가 생성되었고, push도 되었으니 PR를 고려해보라고 메세지가 뜬다.**

    ![image-20211005014652308](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005014652308.png)

    - my) PR는 **`push된 임의의, 새로운 평행우주`를 기본branch로  합치겠습니까? 정도이다.**
    - 브랜치를 바꿔서 들어가보면, **새로운 평행우주가 `로컬-컴1`의 `my-idea`그대로 ** 펼쳐진다.
        ![image-20211005014830772](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005014830772.png)





#### 원격레포의 새로운브랜치를 받는 방법은 [브랜치 생성&전환명령어] 응용: git fetch -> git branch -a -> git checkout -b [생성할 로컬 브랜치(결국엔 뒤에 기입한 원격브랜치를 받아올 브랜치)] [origin(별칭)/pull해올 원격 브랜치명]

- `git checkout -b [생성할브랜치(결국엔 뒤에 기입한 원격브랜치를 받아올 로컬 브랜치)] [origin(별칭)/pull해올 원격브랜치명]`
    - **브랜치 생성&전환 명령어로 원격레포브랜치를 pull까지 해버린다.**
    - `git checkout -b` : 생성과 동시에 전환
    - `[생성할 로컬 브랜치(결국엔 뒤에 기입한 원격브랜치를 받아올 로컬 브랜치)]` : 아직 원격이 새 평행우주(branch)가 없는 로컬에서, 받아줄 브랜치(평행우주)를 지정한다.
    - `[origin/pull해올 원격브랜치명]` : 원격레포 속 받고 싶은 평행우주



- `컴2`에서 원격레포의 새로운브랜치(평행우주)를 받아보자.
    - **굳이 main에 합쳐진 것이 아니어도 받아서 사용해도 된다.**

1. **`컴2`에서 로컬용 `git branch`에 `-a`옵션까지 더해서 `원격 브랜치도 확인`한다**

    ```
    git branch -a
    
    * main
      remotes/origin/HEAD -> origin/main
      remotes/origin/main
    ```

    - **원격branch는 github의 소식을 업데이트를 해줘야한다**

    ```
    git fetch
    git branch -a
    
    * main
      remotes/origin/HEAD -> origin/main
      remotes/origin/main
      remotes/origin/my-idea
    ```

    - 원격레포의 (HEAD, default 작업장소)가 보는 곳은 아니지만 **밑에 새로운 평행우주가 추가되었음**



2. **원격레포의 새로운 평행우주(`my-idea`)를 `컴2`의 `my-dea`로 받아와 보자.**

    - **브랜치 생성&전환명령어로 원격레포브랜치를 pull까지 해버린다.**

    ```sh
    #git checkout -b [로컬브랜치명] [origin/원격브랜치명]
    ❯ git checkout -b my-idea origin/my-idea
    
    Branch 'my-idea' set up to track remote branch 'my-idea' from 'origin'.
    Switched to a new branch 'my-idea'
    ```

    - `git branch`로 확인해보자.

        ```
          main
        * my-idea
        ```





#### 소스트리로 컴2:new브랜치생성 to 원격 to  컴1으로 넘겨보기

1. **이번엔 소스트리로 `컴-2`에서  브랜치를 생성후 `원격레포`에 생성과 동시에 PUSH**

    - `브랜치` 탭 > 새 브랜치 : `my-another-idea`

    - cat이름을 nyang으로 변경

    - **커밋과 동시에 origin푸쉬 by 소스트리**

        ![image-20211005215238143](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005215238143.png)

        - 만약 소스트리가 아니라면?

            ```
            ❯ git add -A
            ❯ git commit -m "EDIT cat name"
            ❯ git push origin my-another-idea
            ```

            - `push origin`은 없으면 해당 브랜치 **생성해서 push**한다.

    - 깃허브에서 branch 생성 확인

        ![image-20211005215523063](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005215523063.png)



2. **`컴-1`의 소스트리에서 `컴2-new브랜치(my-anther-idea)`받아보기**

    - **컴1의 소스트리 > `원격` 탭 클릭 > `원격의 새 브랜치` 더블클릭 > `새 브랜치로 체크아웃` **

        ![image-20211005215806239](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005215806239.png)

    - **`원격 브랜치의 체크아웃`만으로 `로컬의 새브랜치`를 생성 및 가져온다. **

        - 따로 **pull의 과정이 없는 것**이 cli 든 소스트리든 마찬가지다
            ![image-20211005220212543](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005220212543.png)

        





##### my) 새 브랜치는 pull이 아닌 [checkout -b]만으로 로컬에 새 브랜치를 생성 및 내용을 가져온다.





#### 로컬 컴1-main에 브랜치들 merge(자동커밋)후 push -> 컴2에서 fetch로 확인후 받아오기

- `컴1`에서  브랜치병합후  `원격레포`에 push해보자.



1. `main(master)`로 이동(받는놈)

2. 주는놈인 `my-another-idea` 우클릭 > 병합하기

    - `현재 브랜치로 (주는놈) 병합`
        ![image-20211005220439078](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005220439078.png)

3. push할 커밋수 확인해보기

    - 병합전: main옆에 `1`은 push할 커밋수다.

        ![image-20211005221046622](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005221046622.png)

    - 병합(자동커밋!)후: main`2`로 push할 커밋 1개 늘어남.
        ![image-20211005221107701](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005221107701.png)

4. 병합된 내역(커밋)까지 **`컴1 `-> `원격` 레포에 소스트리로 push**

    - push버튼 > 병합된 내역 push
        ![image-20211005221321015](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005221321015.png)



5. **이제 `컴2`의 소스트리에서 `fetch`로 원격상황 받아오기**
    ![image-20211005221459270](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005221459270.png)

    - main브랜치에 pull받을 것들이 있다고 업데이트된다.

        ![image-20211005221522213](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005221522213.png)

    - **`main`으로 이동후 pull받아오기**





### 

### 충돌해결로 협업

- 한쪽에서 파일1을 수정해서 push했는데

    - 다른쪽에서 파일1을 `pull받지 않은 상태`로 **수정후  로컬커밋**한 상태

        ![image-20211005221746958](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005221746958.png)

    - 다른쪽에서 pull받아야하는데, 같은파일이 수정된체 커밋된 상태면, conflict

        ![image-20211005221943054](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005221943054.png)
        ![image-20211005222009972](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005222009972.png)

        



#### 직접 충돌내보기

1. `컴1`에서 `dog`파일 수정, 커밋, push

    - 한쪽 수정(`bark: wang wang`)이 push한 상태

        ```
        ❯ git add -A
        ❯ git commit -m "EDIT dog bark wang"
        ❯ git push
        ```

        

2. **`컴2`에서 pull 받지도 않은 체** `dog`파일 수정후 커밋

    - 다른 쪽이 pull안한체로 수정후 커밋한 상태

        ```
        ❯ git add -A
        ❯ git commit -m "EDIT dog bark warl"
        ```

        ```sh
        ❯ git push
        To https://github.com/is2js/git-github.git
         ! [rejected]        main -> main (fetch first)
        error: failed to push some refs to 'https://github.com/is2js/git-github.git'
        hint: Updates were rejected because the remote contains work that you do
        hint: not have locally. This is usually caused by another repository pushing
        hint: to the same ref. You may want to first integrate the remote changes
        hint: (e.g., 'git pull ...') before pushing again.
        hint: See the 'Note about fast-forwards' in 'git push --help' for details.
        ```

        - **pull할게 있으면 push가 안된다.**

3. **push가 안되면**

    - **`원격에 누가 새 작업을 올렸구나. pull부터 받자`**라고 생각

        ```
        ❯ git pull
        ```



4. **pull받았는데 conflict나면**

    - **`저쪽에서 같은 파일 수정했구나 ㅠㅠ`**라고 생각

    - **`git status`로 더 자세한 상황을 보자.**

        ```sh
        ❯ git status
        
        #...
        
                both modified:   dog
        
        #...저쪽과 나 모두 dog파일을 수정했네..
        ```

    - vscode에서 처리하기

        - `HEAD` : 내가 작성한 부분

        - `===아래부분` : 저쪽이 작성한 부분

            ![image-20211005222814772](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005222814772.png)



5. **conflict 후에는 `git commit` 을 `-m 없이` 커밋하여, 충돌 메세지를 그대로 저장하도록 한다.**

    ```sh
    git add -A
    git commit
    ```

    - `git push`해서 수정본을 원격레포로 보낸다.

    ```sh
    git push
    ```





#### 브랜치삭제, 원격브랜치삭제: git branch -d vs git push -d [origin] [원격브랜치]

1. git branch 목록보기

    ```sh
    git branch -a
    ```

2. 로컬 삭제 & 원격 삭제

    - 로컬 : **`git branch` -d [브랜치명]**
    - 원격 : **`git push` -d origin [브랜치명]**

    ```
    ❯ git branch -d my-idea
    Deleted branch my-idea (was e43a292).
    
    ❯ git push -d origin my-idea
    To https://github.com/is2js/git-github.git
     - [deleted]         my-idea
    ```

    



#### 소스트리로 충돌만들고 해결해보기

- 충돌일으키기 : **pull받기 전, `같은 파일 수정후 로컬 커밋`**
    - push하면 충돌 발생
    - **소스트리는 커밋과 동시에 push하므로 커밋당시에 발생**

- **서로 다른브랜치(평행우주)라도 같은파일 수정상태면 conflict남**



1. pull받기전에, 저쪽에서 수정한 파일을, 나도 수정한 뒤 **소스트리로 커밋&push를 눌러본다.**
    ![image-20211005223551992](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005223551992.png)
    - 커밋은 성공했다고 따로 메세지 뜸.
2. **push시 에러 -> pull받자 -> conflict뜨면? -> 같은파일수정했네**
    - pull을 했더니 **소스트리에서 conflict를 알려준다.**
        ![image-20211005223822604](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005223822604.png)
        ![image-20211005223842396](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211005223842396.png)



3. **`파일상태` 탭으로 가보면, **
    - **`충돌파일`은 `아직 add되지 않은 unstaged된 파일`로서 존재**
    - **`충돌파일 클릭`시 충돌내용이 vscode처럼 나옴**
    - 충돌 커밋메세지는 자동 작성되어있음
        - cli라면 `git commit`만 하란 소리

4. **직접 에디터(vscode)로 가서 수정해줘야한다.**
5. **다시 소스트리로 와서  add후 커밋&push**



##### my) push에러 -> pull받자 -> conflict -> 같은파일 수정했네.







