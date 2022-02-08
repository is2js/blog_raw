---
toc: true
layout: post
description: 우테코 프리코스간 정리한 intellij vscode keymap적용 및 협업 세팅

categories: [java, configuration]
title: intelliJ 세팅(vscode keymap + 협업 세팅)

image: "images/posts/java.png"
---


### vscode keymap 관련 플러그인 설치

- 설정 > Plugin > 
  1. `vscode kemap`검색 후 설치
  2. `key Promoter X` 설치 (해당 기능의 단축키를 우측하단에 띄워줌)

### vscode keymap 기준 단축키 정리

- `shift 2번` : 전체 검색(유지)
  - vscode `F1`

- **keymap 들어가서 `move opposite group`검색후 `ctrl+shift+ 오른화살표`지정**하기
  - `ctrl + \` 이후 넘기기..왼쪽넘기기 기능 자체는 없나보다.
  - `alt+shift+0`으로 세로로 넘기기 가능

- `alt+shift+F`  : reformat code -> 한파일내
  - **패키지 or 폴더 선택후 alft+shift+F**

- **keymap 들어가서 `back`검색후 `ctrl+u`지정**하기

- **`ctrl + .`**으로 메인메소드 내용을 **extract method**
  - **메소드 분리 및 `패키지명 작성하고 폴더이동시키기`**

- **신세계) `alt+insert`로 생성자, getter 등 오버라이딩**

- **신세계) 메서드사용을 위한 객체 만들 때(인스턴스화) 할 때, **
  - **`new 클래스명();` 기입후 `ctrl+.으로 local변수 자동(선택후)생성`하자.**
  - **`객체.메서드();`기입후 `ctrl+.`으로 메서드결과값 받는 변수 자동(선택후)생성**

- **keymap -> extend line selection -> `ctrl+L`을 추가해서 사용하기**
  - 라인 선택하기
    - **shift+alt+아래화살표로 복사할때,, 라인 선택해서해야.. 꼬임없을 듯**
- `shift+ alt+ F10`: **실행할 class파일 선택해서 실행**
  - `ctrl+alt+F10`은 바로 실행.

- `shift + alt + x`: 현재 파일들 다 close

- `shift + alt + a` : 긴글 주석?

- **`alt + ~` : git관련 명령어들 목록나옴**	

- **`ctrl+F11` or `ctrl+shift+{key}`로 북마크 지정**
  - **-> `ctrl+해당번호`로 이동**
  - **keymap -> `show bookmaks`검색후 -> `CTRL+SHIFT + F11 `**으로 지정함

- `ctrl+f3` : 같은단어 이동가능한 `검색한 상태`로 만들기 == `ctrl+f`
  - `f3` : 같은 단어 다음으로 이동
  - `shift+f3` : 같은단어 이전 단어로 이동

- `shift+F5` : 실행중인 **작업 종료**
  - `ctrl+F5` : 실행
  - `shift+ alt + f10` : main함수 골라서 실행

- **`alt+7`: 현재파일 Structure(메소드 등) 한눈에 보기**
  - **유틸메서드로 빼기, 상수 옮기기 : `f6`**으로 편하게

- **리팩토링**
  - **`alt+ctrl+c` : 값을 상수로 등록해버리기**
  - **`alt+ctrl+m` : 코드를 메서드화 시키기**
    - 2번 눌르면 창으로 하는 듯
  - **`alt+ctrl+v`: `new 클래스()`까지 입력후 참조변수 바로 생성**

- **축약어**
  - iter
  - `fori` 추가
  - itar
  - ifn
  - inn

- **`F8`: 오류바로 찾아가기**
- `ctrl+f` -> `F3` or `shift + F3` : 검색항목 순서대로 찾아가기


### 프리코스를 위한 java8 협업 세팅 요약본

#### clone후 branch부터 생성후 바꾸기

```
git checkout -b is2js
```

#### git CRLF -> LF로 바꾸기 설정하기

```
git config --global core.autocrlf true
```

#### utf 8 설정

- utf8, lf설정 되어있나 확인하기

  - **editor > `File Encodings` > 체크 3가지 모두 utf-8**
    - shift 2번연속 클릭 (search everywhere)

      - `vm`검색
        - Edit Custom VM option 클릭
          - **맨 마지막 줄에 `-Dfile.encoding=UTF-8`  추가**

- build.gradle 설정 추가

  ```
  compileJava.options.encoding = 'UTF-8' // UTF-8 설정
  compileTestJava.options.encoding = 'UTF-8'
  ```

  


#### code style 3가지 / Checkstyle / saveActions

- **`code style`에서** 

  1. **`scheme지정`**
  2. **`\n`으로 선택해주기**
  3. **`detect하도록도 체크`함..**
  4. `java`탭으로 내려가서 >  scheme 지정확인![image-20211129215045829](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211129215045829.png)
     ![image-20211213200619247](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211213200619247.png)
     - checkstyle에 안걸리게 됨.
       ![image-20211130110221537](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211130110221537.png)

- Tools > **`CheckStyle`** 

  1. 8.24이상 버전 (default)

  2. **scope를 `All sources(테스트포함, 나는 테스ㅡㅌ제외)`**로 변경

  3. **파일 선택(일부코드 삭제하여 수정한)하여 추가 -> `+로 추가`**

     ![image-20211213201718965](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211213201718965.png)

  4. 해당 스타일체크후 **`alt +shift + k`혹은 하단 체크스타일**에서 **추가한 naver style선택**하여두기
     ![image-20211213202004921](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211213202004921.png)


- 다 풀고 **맽밑 Other settings > `Save Actions` > 3가지 체크 **

  ![image-20211213201047822](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211213201047822.png)



#### java8로 통일하기

1. 설정> Build> Build Tools > **`Gradle`** > **intelliJ IDEA +  Gradle JVM 1.8**로 바꾸기
   - exception 및 디버깅용으로 gradle 변경하는 것
2. 설정> Build> Build Tools > **Compiler > `Java Compiler`** > javac의 1.8 바꾸기
3. 프로젝트구조(c +s + a + s)  >**Project** > **`Project SDK 및 language level 1.8, 8 `**선택
4. 프로젝트구조(c +s + a + s)  > **Modules > `랭귀지레벨`**에서 8버전 맞나 확인하기
5. 프로젝트구조(c +s + a + s)  > **SDKs**에서 1.8버전 확인하기

#### window intellij에서 gradle clean, test 해보기

![image-20211206150651962](https://raw.githubusercontent.com/is3js/screenshots/main/image-20211206150651962.png)

1. `shift 2번` or `f1` -> `gradle task선택`
   1. `gradle clean` 수행
   2. `gradle test` 수행
      1. **코드 다짜고나서**

