---
toc: true
layout: post
description: windows10에서 wsl2를 이용한 python 개발환경 구성하기
categories: [configuration]
title: win10에서 Terminal + vscode + Wsl2 + Pyenv작업환경 구성

image: "images/posts/python.png"
---

### chocolately로 초기 설치

1. 관리자권한 powershell

    - 초코랫리 겟스타트 -> 인스톨 코드 복붙
    - 경고떠도됨. 관리자권한으로 다시 껐다 키기

2. find pakage에서 **terminal** 검색후 설치

    > choco install microsoft-windows-terminal

3. **python** 3 검색후 3.9 설치

    >choco install python3

4. **vscode** 설치

    > choco install vscode

5. git 설치

    > choco install git
    >
    > > git config --global user.name "2js"
    > >
    > > git config --global user.email "1@gmail"

6. wsl2 설치 -> **재부팅후 한번 더 실행** (-y -f ??)

    > choco install wsl2 -y -f 

    - [참고사이트](https://askubuntu.com/questions/1264102/wsl-2-wont-run-ubuntu-error-0x80370102)

    - 참고) 정식 가이드로 설치시 해주는 다운로드전 설정

        > ```shell
        > dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
        > # WSL 활성화
        > dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
        > # VM platform 활성화
        > ```

7. **관리자 권한으로 윈도우 가상화해주기 for wsl2**

    > Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
    >
    > bcdedit /set hypervisorlaunchtype auto

    

8. **AMD CPU SVM mode 설정 in Bios** for ubuntu 실행

    - del 로 bios 진입

    - advanced 진입(F7)

    - OC(Overclock) 진입

    - advanced cpu configuration 진입

    - **SVM node: Enable**

        - 우측상단 X로 저장후 종료하기

              

9. wsl2용 ubuntu1804 설치 -> **ms store에서 설치(choco는 update안된 버전이라고함)**

    - msstore 사용시 ms계정 요구함
        - ~@hanmail.ent  / ~54
    - 설치 -> 시작



10. `wsl`명령어로 우분투 진입
    - default 계정 및 비번 설정
        - is2js / 
        - ~213





11. **항상 `wsl`접속으로 /mnt된 곳이 아니라 `cd ~` (linux 홈폴더)를 한 상태에서 `code ~/.zshrc` 등의 설정을 하자.**





#### Window Terminal 단축키 설정

- vscode와 비슷하게 추가 매핑해준다.

    - actions 부분에 추가해주면 된다.

    ```json
            {
                "command": "find",
                // 검색키 매핑
                // "keys": "ctrl+shift+f",
                "keys": "ctrl+shift+f"
            },
            // 수직분할 매핑(추가)
            // { "command": { "action": "splitPane", "split": "vertical", "splitMode": "duplicate" }, "keys": "alt+shift+plus" },
            { "command": { "action": "splitPane", "split": "vertical", "splitMode": "duplicate" }, "keys": "ctrl+\\" },
            // 수평분할 매핑(추가)
            //{ "command": { "action": "splitPane", "split": "horizontal", "splitMode": "duplicate" }, "keys": "alt+shift+-" },
            { "command": { "action": "splitPane", "split": "horizontal", "splitMode": "duplicate" }, "keys": "alt+shift+0" },
            // 탭 끄기 매핑(추가)
            { "command":  "closePane", "keys": "ctrl+w" },
            // 포커스key 매핑(추가)
            // { "command": { "action": "moveFocus", "direction": "down" }, "keys": "alt+down" }, 
            { "command": { "action": "moveFocus", "direction": "down" }, "keys": "ctrl+alt+down" }, 
            { "command": { "action": "moveFocus", "direction": "left" }, "keys": "ctrl+alt+left" }, 
            { "command": { "action": "moveFocus", "direction": "right" }, "keys": "ctrl+alt+right" }, 
            { "command": { "action": "moveFocus", "direction": "up" }, "keys": "ctrl+alt+up" },
    
            // 창이동 단축키 매핑(추가)
            // { "command": { "action": "switchToTab", "index": 0 }, "keys": "ctrl+alt+1" },
            { "command": { "action": "switchToTab", "index": 0 }, "keys": "ctrl+1" },
            { "command": { "action": "switchToTab", "index": 1 }, "keys": "ctrl+2" },
            { "command": { "action": "switchToTab", "index": 2 }, "keys": "ctrl+3" },
            { "command": { "action": "switchToTab", "index": 3 }, "keys": "ctrl+4" },
            { "command": { "action": "switchToTab", "index": 4 }, "keys": "ctrl+5" },
            { "command": { "action": "switchToTab", "index": 5 }, "keys": "ctrl+6" },
            { "command": { "action": "switchToTab", "index": 6 }, "keys": "ctrl+7" },
            { "command": { "action": "switchToTab", "index": 7 }, "keys": "ctrl+8" },
            { "command": { "action": "switchToTab", "index": 8 }, "keys": "ctrl+9" },
            // Rename a tab to "Foo"
            { "command": { "action": "renameTab", "title": "server" }, "keys": "f1" },
            { "command": { "action": "renameTab", "title": "jupyter" }, "keys": "f2" },
    ```

    





### Window Terminal 및 wsl2 zsh 세팅



1. 터미널에서 설정(ctrl+,) > json파일열기 > ubuntu이름을 wsl2로 바꾸고

    - wsl2(ubuntu)의 guid를 복사해서 > `defaultProfile` key에 넣어 **기본 프로필로 설정**

    

2. Fira code설치후, WindowTerminal 및 vscode-editor + vscode terminal font 변경해주기

    - [깃허브](https://github.com/tonsky/FiraCode) > zip파일 다운후 압축풀고 > ttf 다 설치

        - Terminal : **없던 `defaults`속 `fontFace`속성을 넣어서 폰트를 설정해준다.**

        - Vscode : font 검색후, 맨앞에 **`"Fira code"` 추가** 후 전체복사

            - terminal font 검색 후 복붙

                > "Fira code", Consolas, 'Courier New', monospace

3. zsh설치(**zsh는 root로 설치**)

    > sudo apt update && upgrade
    >
    > sudo apt install zsh



4. oh my zsh(zsh) 설치 (**ohmyzsh부터는 user로 설치?!**)

    - [github](https://github.com/ohmyzsh/ohmyzsh) 들어가서 curl로 설치 명령어 날리기

        > sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"



5. ohmyzsh 플러그인 2개(자동완성, 하이라이팅) 설치

    > ###### zsh-syntax-highlighting
    >
    > git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
    >
    > ###### zsh-autosuggestions
    >
    > git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

    - .zshrc에 기입해주기

        > code ~/.zshrc
        >
        > > plugin 검색
        > >
        > > plugins=(git) 에 공백으로 추가 해주기
        > >
        > > plugins=(git zsh-syntax-highlighting zsh-autosuggestions)
        >
        > 나와서 
        >
        > source ~/.zshrc

6. p10k(ohmyzsh의 테마) 설치

    - [깃허브](https://github.com/romkatv/powerlevel10k) 에서 ohmyzsh 클릭 후 ohmyzsh 하위폴더로 clone시켜주기

        > ```sh
        > git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
        > ```

    - **p10k를 zsh의 테마로 설정해주기**

        > >  code ~/.zshrc
        >
        > 
        >
        > theme검색후  ZSH_THEME="robbyrussell"를 
        >
        > > ZSH_THEME="powerlevel10k/powerlevel10k"
        >
        > 로 변경
        >
        > > source ~/.zshrc

    - `p10k configure `설정이 시작됨



7. `ls -al` 등이 파일명 배경색 직접 변경하기

    > cd ~
    >
    > > code ~/.zshrc
    >
    > 
    >
    > > \# change filname background color 
    > >
    > > LS_COLORS="ow=01;36;40" && export LS_COLORS
    >
    >  위 내용 추가
    >
    > > source ~/.zshrc



8. Win-terminal 테마 생성 및 설정해주기

    - `terminalsplash.com`에 들어가서 고른다.

        - `VS code theme`를 찾음. 아직 json내부에서 `"name"`이 없는 상태.

    - `"profiles"` 밑에 `"schemes"`에 **name을 추가해주면서 ** custom theme를 등록해야한다.

        - `schemes `에 등록한 뒤, `colorScheme`로 사용된다

        ```json
            "schemes": [
        		
        		{
        			"name" : "VScode",
        			"background" : "#232323",
        			"black" : "#000000",
        			"blue" : "#579BD5",
        			"brightBlack" : "#797979",
        			"brightBlue" : "#9BDBFE",
        			"brightCyan" : "#2BC4E2",
        			"brightGreen" : "#1AD69C",
        			"brightPurple" : "#DF89DD",
        			"brightRed" : "#F6645D",
        			"brightWhite" : "#EAEAEA",
        			"brightYellow" : "#F6F353",
        			"cyan" : "#00B6D6",
        			"foreground" : "#D3D3D3",
        			"green" : "#3FC48A",
        			"purple" : "#CA5BC8",
        			"red" : "#D8473F",
        			"white" : "#EAEAEA",
        			"yellow" : "#D7BA7D"
        		},
        		
        	],
        ```

    - wsl2(ubuntu)와 win-terminal or **terminal 전에 colorScheme**으로 지정해준다.

        ```json
        		    {
                        "guid": "{c6eaf9f4-32a7-5fdc-b5cf-066e8a4b1e40}",
                        "name": "Wsl2",
                        "source": "Windows.Terminal.Wsl",
        				"colorScheme" : "VScode",
                    },
                    {
                        // Make changes here to the powershell.exe profile.
                        "guid": "{61c54bbd-c2c6-5271-96e7-009a87ff44bf}",
                        "name": "Windows PowerShell",
                        "commandline": "powershell.exe",
                        "hidden": false,
        				"colorScheme" : "VScode",
                    },
        ```

        ```json
            "profiles":
            {
                "defaults":
                {
                    // Put settings here that you want to apply to all profiles.
        			"fontFace" : "Fira Code",
        			"colorScheme" : "VScode",
                },
        ```







### wsl2 계정 및 폴더 설정 in win10

#### 항상 특정유저로 wsl2 진입

- `/etc/wsl.conf` 없으면 생성

    ```sh
    sudo vi /etc/wsl.confg
    ```

    ```sh
    [user]
    default=is2js
    ```



#### 윈도우 탐색기에 wsl2경로 네트워크로 만들기

- **윈도우(\\)탐색기 창**에서는 `\\wsl$` 로 우분투로 접속가능하다.
    - **config등 경로**에서는 `//wsl$`의 슬래쉬로 사용된다.
    - 참고) 윈도우탐색기상 **`wt -d .`을 통해, 현재경로에서 `윈도우 터미널 열기`**



1. **`wsl 쉘` 킨 상태로 연결유지**
2. `탐색기` > `내PC`  우클릭 > `네트워크 위치 추가`
    - `사용자 지정 네트워크 위치 선택`
    - [인터넷 또는 네트워크주소]  부분에 **`\\wsl$`입력후 `찾아보기`**
    - 원하는 경로선택하기 : `우분투/home/is2js/workspace/`
    - 원하는 이름 입력 : `is2js_workspace`
        ![image-20211001225221185](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211001225221185.png)
3. 여기서 들어간 뒤 `wt -d .`로 윈도우 터미널(wsl)을 열면 될듯?







### wsl2 ubuntu 기본설정

- [루트 계정 비밀번호 초기화참고 블로그](https://studyforus.tistory.com/223)
- [유저계정을 root 화시키기 참고 블로그](https://jcil.co.kr/24)
    - [usermod -aG sudo [계정명]으로만 주기](https://extrememanual.net/10697)



1. openssl 설치

    >  sudo apt install openssl

2. **root의 기본 비밀번호 초기화**

    > sudo passwd root

    - `#` : 관리자
    - `$` : 일반유저



#### 유저계정에 root권한부여 (파일3개 수정)

1. **root계정으로 권한설정파일에 유저계정-> root권한부여**

    >su
    >
    >vi /etc/sudoers

    - 아래 root쪽에 내 계정도 추가하기

        ```sh
        # User privilege specification
        root    ALL=(ALL:ALL) ALL
        is2js   ALL=(ALL:ALL) ALL
        ```

    > wq! (readonly떠서 강제로 저장후 종료)

2. **root계정으로 비밀번호설정파일에 유저계정-> root권한부여**

    - root항목처럼, `root:x:0:0:root:`/~f로 시작하는 부분에서
        - 앞쪽0 : uid
        - 뒤쪽0: gid
            - 관리자라면 둘다 0임.
            - **나는 뒤쪽 그룹id만 0으로줌.**

    > vi /etc/passwd
    >
    > wq!

    ```sh
    #is2js:x:1002?:1002?:,,,:/home/is2js:/usr/bin/zsh
    is2js:x:1002?:0:,,,:/home/is2js:/usr/bin/zsh
    ```



3. **root계정으로 그룹설정파일에 유저계정-> root권한부여**

    - root가 대장인 0그룹에 **추가를 해주면됨**

        > vi /etc/group
        >
        > wq!

        ```sh
        #root:x:0:
        root:x:0:is2js
        ```

        

4. zsh 권한 에러시

    ```sh
    sudo chown -R ${LOGNAME}:staff $HOME
    ```

5. **wsl2-ubuntu 시작계정 설정하기**

    - `powershell` 에서

        ```powershell
        ubuntu[1804] config --default-user [is2js]
        ```

6. wsl2-ubuntu **시작폴더 설정**하기 in `WindowTerminal`

    - 터미널의 wsl프로필에다가 옵션을 추가해줌

        - [출저](https://myskan.tistory.com/124)

        ```json
        "startingDirectory":"\\\\wsl$\\Ubuntu-[20.04]\\home\\[myskan]"
        ```

        ```json
        "guid": "{c6eaf9f4-32a7-5fdc-b5cf-066e8a4b1e40}",
        "name": "Wsl2",
        "source": "Windows.Terminal.Wsl",
        "colorScheme" : "VScode",
        "startingDirectory":"\\\\wsl$\\Ubuntu-18.04\\home\\is2js",
        ```

    - 참고 

        - `\\wsl$`  : 탐색기에서 우분투 폴더라도 바로 가기





#### python->python3 매핑

- [참고블로그](https://codechacha.com/ko/change-python-version/)
- [더 좋은 블로그](https://seongkyun.github.io/others/2019/05/09/ubuntu_python/)



1. Alternatives는 **기본 커맨드의 심볼릭 링크를 관리**해주는 리눅스 프로그램입니다. 데비안 계열의 리눅스(**우분투)에서는 `update-alternatives`가 제공**됩니다.



2. 우분투(Ubuntu)를 설치하면 python path가 2.7로 설정되어 있습니다. 

    - 리눅스의 `Alternatives`를 이용하면 python 버전을 쉽게 변경하고 관리할 수 있습니다.

    ```sh
    ❯ which python
    
    /usr/bin/python
    ```

    - **`python`은 `/usr/bin/python`의 링크인데**

    ```sh
    ❯ ls -al /usr/bin/ | grep python
    
    lrwxrwxrwx  1 root   root           9 Apr 16  2018 python -> python2.7
    lrwxrwxrwx  1 root   root          16 Apr 16  2018 python-config -> python2.7-config
    lrwxrwxrwx  1 root   root           9 Apr 16  2018 python2 -> python2.7
    lrwxrwxrwx  1 root   root          16 Apr 16  2018 python2-config -> python2.7-config
    -rwxr-xr-x  1 root   root     3633000 Feb 28  2021 python2.7
    lrwxrwxrwx  1 root   root          33 Feb 28  2021 python2.7-config -> x86_64-linux-gnu-python2.7-config
    lrwxrwxrwx  1 root   root           9 Oct 25  2018 python3 -> python3.6
    ```

    ```sh
    ❯ ls -al /usr/bin/python
    
    lrwxrwxrwx 1 root root 9 Apr 16  2018 /usr/bin/python -> python2.7
    ```

    - **`/usr/bin/python` 역시 실행파일이 아닌  `/usr/bin/python2.7`의 링크이다.**

    - **즉 **
        - `/usr/bin/python2.7` -> `/usr/bin/python`  -> `python`



3. **`--config python`로 `python`에 걸려있는 링크를 확인한다.**

    ```sh
    sudo update-alternatives --config python
    
    [sudo] password for is2js:
    update-alternatives: error: no alternatives for python
    ```

    - alternatives가 없다고 뜨면, 설정을 해줘야한다.



4. **`--install [symbolic link path(버전을 제외한 공통 경로)] python [real path(버전명시 경로)] [우선순위number]`로  여러버전을 등록한다.**

    ```sh
    #sudo update-alternatives --install /usr/bin/python(버전제외 공통 경로) python(명령어=링크) /usr/bin/python3(버전명시 경로) 10(우선순위)
    ```

    

    - ~~python2.7 등록~~

        ```sh
        sudo update-alternatives --install /usr/bin/python python /usr/bin/python2.7 2
        ```

    - python3.6 등록

        - **3.6 한개만 등록하고 해당 버전을 선택안해도 python -> python3.6이 걸리도록 하면 된다.**

        ```sh
        sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.6 1
        ```

    

5. (만약 2개이상 버전 등록했다면) 다시 한번 **`--config python`으로 등록된 버전** 확인 및 **선택**

    ```sh
    sudo update-alternatives --config python
    
    There are 2 choices for the alternative python (providing /usr/bin/python).
    
      Selection    Path                Priority   Status
    ------------------------------------------------------------
    * 0            /usr/bin/python2.7   2         auto mode
      1            /usr/bin/python2.7   2         manual mode
      2            /usr/bin/python3.6   1         manual mode
    
    Press <enter> to keep the current choice[*], or type selection number:
    
    
    2
    ```

    

6. **버전을 선택해주면, `python`의 링크가 특정버전으로 옮겨져있다. **

    ```sh
    ❯ python -V
    Python 3.6.9
    ```

    

7. **pip도 설정해준다.**

    - **1개만 등록하면 pip -> pip3가 될 것이다.**

    ```sh
    ❯ sudo update-alternatives --config pip
    
    update-alternatives: error: no alternatives for pip
    ```

    ```sh
    ❯ sudo update-alternatives --install /usr/bin/pip pip /usr/bin/pip3 1
    
    update-alternatives: using /usr/bin/pip3 to provide /usr/bin/pip (pip) in auto mode
    update-alternatives: warning: not replacing /usr/bin/pip with a lin
    ```

    ```sh
    ❯ pip -V
    
    pip 9.0.1 from /usr/lib/python3/dist-packages (python 3.6)
    ```

    



- **1개만 alternative를 등록해서 python, pip를 default로 3버전으로 지정해주자.**

```sh
# pip3 install
sudo apt-get update
sudo apt-get install -y python3-pip
pip3 --version

# python3, pip3 alternatives command
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 10
sudo update-alternatives --install /usr/bin/pip pip /usr/bin/pip3 1
```









### python 가상환경 설정



#### pip3 & pyenv(격리된 python) 설치

1. python3-pip 설치

    ```sh
    sudo apt update && upgrade
    
    sudo apt install python3-pip
    
    pip3 -V
    pip3 --version
    ```

    

2. pyenv 설치

    ```sh
    curl https://pyenv.run | bash
    ```

    ```
    WARNING: seems you still have not added 'pyenv' to the load path.
    
    
    # See the README for instructions on how to set up
    # your shell environment for Pyenv.
    
    # Load pyenv-virtualenv automatically by adding
    # the following to ~/.bashrc:
    
    eval "$(pyenv virtualenv-init -)"
    ```

    - clone한 pyenv디렉토리를 zshrc에 환경변수(`PYENV_ROOT`)로  직접지정해줘야한다.

        ```sh
        cd ~ # wsl로 정상적으로 열릴려면, /mnt/상태에서 vscode를 열면 안된다.
        
        code ~/.zshrc
        ```

        ```sh
        source ~/.zshrc
        ```

    - pyenv사용전, 에러를 일으키는 미설치 패키지 다 설치하기

        ```sh
        sudo apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev \
        libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev libncursesw5-dev \
        xz-utils tk-dev
        ```





3. pyenv 사용법

    ```sh
    # 설치가능한 python list 확인하기
    pyenv install --list
    
    # 원하는 python 버전 설치
    pyenv install [파이썬버전]
    
    # 설치된 파이썬 버전 확인
    pyenv versions
    
    # 가상환경 만들기
    pyenv virtualenvs [파이썬버전] [가상환경명]
    
    # 가상환경 시작하기
    pyenv activate [가상환경명]
    
    # 가상환경 끝내기
    pyenv deactivate
    
    # 가상환경 목록 확인
    pyenv virtualenvs
    
    # 가상환경 삭제하기
    pyenv uninstall [가상환경명]
    
    ### 실사용
    pyenv versions
    
    pyenv install 3.8.6
    
    pyenv global 3.8.6 # 3.8.6을 pyenv 기본 python으로 지정
    ```

    - python 저장위치
        - root -> `/root` 가 홈폴더
            - 유저의 경우 -> `/home/유저명`이 홈폴더
        - `~/.pyenv/` 라는 곳에 `versions/`폴더에 설치된다.
            - github다.
        - virtualenvwrapper에서도 `~/.pyenv/versions/3.8`에서 자동완성후 `/3.8.6/bin/python`을 이용한다.



#### virtualenv & pyenv-virtualenvwrapper(격리된 python으로 가상환경 생성) 설치



1. **pip3로 `virtualenv` `virtualenvwrapper` 2개 패키지 설치**

    ```sh
    pip install virtualenv virtualenvwrapper
    pip3 install virtualenv virtualenvwrapper
    
    #에러나므로 --upgrade까지
    pip install --upgrade virtualenvwrapper
    pip3 install --upgrade virtualenvwrapper
    ```

2. **virtualenvwrapper** 설정

    ```sh
    # virtualenv 설정
    export WORKON_HOME=~/.virtualenvs
    export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python # /usr/bin/python3
    ```

    

3. **pyenv-virtualenvwrapper 설치**

    ```sh
    git clone https://github.com/pyenv/pyenv-virtualenvwrapper.git $(pyenv root)/plugins/pyenv-virtualenvwrapper
    ```

    - pyenv설정 추가 수정

        ```sh
        # pyenv clone후 경로 직접 지정
        export PYENV_ROOT="$HOME/.pyenv"
        export PATH="$PYENV_ROOT/bin:$PATH"
        if command -v pyenv 1>/dev/null 2>&1; then
          eval "$(pyenv init -)"
        fi
        
        pyenv virtualenvwrapper_lazy  #에러나서 주석처리했다가 다시 풀어줌
        ```

        ```
        source ~/.zshrc
        ```

    - virtualenv 추가 설정

        ```sh
        # virtualenv 설정
        export WORKON_HOME=~/.virtualenvs
        export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python
        #source /usr/local/bin/virtualenvwrapper.sh
        source ~/.local/bin/virtualenvwrapper.sh
        ```





4. 실사용

    ```sh
    pyenv install 3.8.6
    pyenv versions
    #~/.pyenv/versions/ 에 격리된 파이썬들 버전명의 폴더에 설치됨.
    pyenv global 3.8.6          
    pyenv versions
    
    mkvirtualenv goglekaap
    which python
    #/home/is2js/.virtualenvs/goglekaap/bin/python
    
    deactivate
    
    workon
    # goglegkaap
    rmvirtualenv goglekaap
    
    # pyenv로 만든 python버전을 지정해서 가상환경 생성
    mkvirtualenv -p ~/.pyenv/versions/3.8.6/bin/python goglekaap
    
    lsvirtualenv # workon이랑 동일한 듯.
    ```

    

    ```sh
    # pyenv
    pyenv install [version] # Python 버전 설치
    pyenv versions # 현재 설치된 Python 버전 목록
    pyenv global [version] # 전역 Python 버전 변경
    
    #virtualenv wrapper
    mkvirtualenv [project name] # 프로젝트를 위해 virtualenv 생성
    workon [project name] # virtualenv 활성화
    deactivate # virtualenv 비활성화
    setvirtualenvproject # 현재 디렉토리를 프로젝트의 Home 디렉토리로 설정하기
    rmvirtualenv [project name] # virtualenv 삭제
    lsvirtualenv # virtualenv 목록
    
    
    mkvirtualenv [가상환경이름]
    mkvirtualenv [-p ~/.pyenv/versions/3.8.6/bin/python] [tester(가상환경이름)]
    #가상환경이름으로 가상환경을 만들어준다. mkvirtualenv test -p [python-path]로 python 버전을 지정해 줄 수 있다.
    
    rmvirtualenv [ 가상환경이름]
    #해당 가상환경을 지운다.
    
    workon [가상환경이름]
    #해당 가상환경으로 진입한다.
    
    setvirtualenvproject
    # <가상환경 진입상태 by workon>에서 프로젝트 디렉토리로 이동 후, 해당 커맨드를 치면 디렉토리<--> 가상환경이 맵핑된다. 그러면 workon커맨드를 치는것 만으로 해당 디렉토리에 이동까지 시켜준다. 아주좋다.
    
    cdproject
    #setvirtualenvproject를 했을 경우, 현재 어디에 있든 해당 커맨드로 프로젝트 디렉토리에 이동이 가능하다.
    # 예를 들어, 아무폴더에서 workon [가상환경] -> cdproject
    
    deactivate
    #가상환경에서 빠져나온다.
    ```

    



#### 참고) path 관련 확인해보기

**python 설치(PATH) 확인**

- linux에서는 `python + tab`, `python3 + tab`을 통해 깔린 버전을 간접적으로 파악 가능
    - python3로 자동완성된다면.. python3만 깔린 것 
        - 앞으로 모든 명령어 앞에 python`+3`
- `python3 --help`
    - `-m [모듈명]` : mod로서, **library모듈**을 script로 실행시키는 옵션
        - pip, flask, venv 등에서 쓰임. 물론 패키지가 깔린상태여야함.
        - `python3 -m venv [가상환경명]`
    - `-V` : 버전확인
    - `-c " python코드 "` : python코드를 바로 커맨드라인에서 실행함



**win10 환경변수 path로 설치확인**

1. `win+s` 에서  `환경 변수`검색 > **`시스템 환경 변수 편집`**

2. 고급탭 > **`환경변수N` > 사용자 변수 or 시스템 변수**

    - 나는 초콜렛리로 설치하였더니 `시스템 변수`탭에 `Path`에 설치됨.
        - `C:\Python39\Scripts\` : 스크립트 폴더를 더블클릭해서 가보기
        - `C:\Python39\`

    - ![image-20210926221252849](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210926221252849.png)
        ![image-20210926221506408](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210926221506408.png)





**win10 or wsl2 python os모듈로 확인**

1. import os

2. os.environ (dict)

    - os.environ.get('SHELL') for `wsl2`

        ```python
        os.environ.get('SHELL')
        '/usr/bin/zsh'
        ```

        





**python path로 실행가능한 python script만들기(앞에 python3 안달아도됨)**

- `which python3` 후 경로를 복사해놓는다.

    - `/usr/bin/python3`

- python 파일의 가장 위에 `#!` + `python경로`를 입력한 뒤, python 코드를 작성하면된다.

    - `code calculate.py`

        ```python
        #!/usr/bin/python3
        
        def add(x,y):
            return x+y
        
        print(add(2,3)) 
        ```

        - 결과물은 print만 되도록한다.(bash실행이라서?)

    - `sudo chmod +x calculate.py`

    - **실행시 calculate.py가 아니라 ./calculate.py로 실행시킨다.**

        - `./calculate.py`
        - 에러 뜨면 `sed -i 's/\r$//' calculate.py` 실행후 다시 실행



