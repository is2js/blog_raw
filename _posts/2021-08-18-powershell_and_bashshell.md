---
toc: true
layout: post
description: 자주 사용하는 powershell, bashshell명령어 학습
categories: [shell, powershell, WindowsTerminal, windows]
title: powershell_vs_bashshell명령어
---

## 개요

- 출저 : [유튜브-드림코딩](https://youtu.be/EL6AQl-e3AQ)

- 참고 : [티스토리 리눅스 명령어모음 블로그](https://webdir.tistory.com/155)

- 나는 Windows Terminal을 다운 받은 뒤, powershell vs wsl에서 실습하였다.
  ![image-20210818234123664](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210818234123664.png)

  - window의 powershell은 alias를 제공하여 unix shell과 비슷한 명령어를 사용할 수 있다.

    ![image-20210818233017505](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210818233017505.png)

- 아직 많은 개발자들이 window를 사용함

  ![image-20210818232959450](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210818232959450.png)

### powershell 최신버전 설치

- 링크 : https://docs.microsoft.com/ko-kr/powershell/scripting/install/installing-powershell-core-on-windows?view=powershell-7.1

## 기본 명령어

### man, clear

- manual의 약자다. 잘 모를 때 참고하는 명령어

  ```powershell
  man man
  ```

- Get-Help 명령어를 링크 해놓은 것이다.

- 청소해주는 명령어

```
man clear
clear
```

## 파일 탐색

### pwd(경로 확인)

- Print Woring Directory의 약자
  ![image-20210818234328441](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210818234328441.png)

### explorer.exe .(경로상 탐색기열기)

- mac에서는 `open .`을 사용한다.

- ~~`explorer`가 아니라~~ `explorer.exe .`연다.

### ls [하위폴더] (경로상 파일목록)

- list의 약자

- powershell에서는 mode까지 같이 나오는 반면,

  - powershell은 `ls`만 가능하다. `ls -l`등의 format활용은 X. 처음부터 다 보여준다.

  - wsl/mac에서는 파일명만 나온다.
    - `ls -al`, `ls -atl` 등을 활용하면 mode까지 같이 출력된다.\

  ![image-20210818235011752](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210818235011752.png)

```powershell
ls
```

#### format for WSL2

- `ls -l` : long의 약자

  ![image-20210818235501721](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210818235501721.png)

- `ls -a` : all의 약자

  - ui상에서는 보여주지 않는 숨겨진 폴더, 파일을 보여준다.

  ![image-20210818235601660](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210818235601660.png)

- `ls -al` : all(숨겨진) + long(mode까지)

  ![image-20210818235642602](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210818235642602.png)

#### format for Powershell

- powershell의 `ls`는 기본적으로 long모드로 보여주는 듯 했다.

- `ls -name` : 이름만 보고 싶을 때

  - mac, wsl의 `ls`기본과 똑같다.

- `ls -force` : 숨겨진 폴더, 파일을 보고 싶을 때

  ![image-20210818235955207](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210818235955207.png)

## 폴더이동

### cd [./../-/~]

- change directory의 약자

  - `.` : 현재 경로. `cd .`는 제자리 이동.

  - `..` : 상위 경로

  - `~` : **`현재설정된 사용자`의 최상위 폴더 = `home` 디렉토리 **

    - powershell에서도 `cd ~`를 하면, C:\Users\\`사용자`
    - wsl에서 `cd ~`를 하면,
      - root계정 : /`root `
      - 특정사용자 : /Users/`사용자`

    ![image-20210819001352158](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210819001352158.png)

  - `-` : **직전경로**

    - powershell은 `cd -`가 안된다. -> 7.14버전으로 업데이트 해보는 중.

      - 7버전으로 업데이트하면 된다...

        <img src="https://raw.githubusercontent.com/is3js/screenshots/main/image-20210819002304156.png" alt="image-20210819002304156" style="zoom:50%;" />

      - 대신, **직전경로 1번만 사용가능하며, 이후 `C:\`로 이동된다..**
        ![image-20210819003901036](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210819003901036.png)

## 파일, 디렉토리 찾기

### find [경로] -type [f/d] -name "\*.\*" for shell

- 입력한 경로부터 시작하여 하위까지 해당 파일or폴더를 찾는다.

  - 예시

    - 나같은 경우 -type file , -type directory해도 안되길래, f, d로 약어로 넣으니 됬다.
    - **permission denied나니까, 관리자 모드로 접속하자.**

    ```shell
    find . -type d -name "*flask*"
    ```

    ```
    find . -type f -name "*.csv"
    ```

### get-childitem for powershell

- powershell사실상 안된다고 보면 된다.
  - `get-childitem -File -Filter "*.txt" -Recurse`

## 프로그램 설치/설정 위치

### which for shell

- 프로그램의 실행경로를 확인한다.

  ```shell
  which node
  which code
  ```

### gcm for powershell

- get-command의 약자인 gcd를 이용한다.

- 경로는 창을 키워야 보임

  ```powershell
  gcm node

  gcm code
  ```

## 파일 생성 및 관리

### touch [파일명] (파일생성)

- 존재하지 않으면, 파일 생성

```shell
man touch

touch new_file1.txt
```

- 이후 new_file1.txt를 열어 파일

#### new-item [파일명] for powershell

- touch의 생성은 powershell에 없다. 하지만 cat, echo는 존재함.

```powershell
new-item new_file4.txt

cat new_file4.txt
```

### cat [파일] [파일2] (파일내용확인)

- touch와 달리, **powershell에도** 사용가능함(alias존재)

- touch로 생성한 파일의 내용을 빠르게 확인
  - 여러 파일을 동시에 쓰면, 붙혀서 string으로 확인됨.

```shell
touch new_file2.txt

cat new_file2.txt
second line#

cat new_file1.txt new_file2.txt

first line
텍스트 추가second line#
```

### echo "문자열" >(>>) [파일] (>파일생성+컨텐츠입력/ >> 컨텐츠 추가)

- 기본사용은 작성한 문자열을 터미널에 출력한다. (터미널에 에코~) 거의 사용안한다.
- powershell에서도 사용가능하다.
- **`> [파일명]`**을 통해 **파일생성+해당문자열을 컨텐츠로 넣어줌(`덮어쓰기`)**

```shell
echo "third line" > new_file3.txt

cat new_file3.txt
third line
```

```sh
echo "third line---" > new_file3.txt

cat new_file3.txt
third line---
```

- **`>> [파일명]`** : **파일생성+덮어쓰기가 아니라 `기존파일에 컨텐츠 append`**개념이다.

```sh
echo "third line-----" >> new_file3.txt

cat new_file3.txt
third line---
third line-----
```

## 디렉토리 생성

### mkdir -p [폴더/하위폴더/하하위폴더]

- make directory의 약자, 폴더 = 경로를 생성하는 명령어
- powershell 공용

```sh
mkdir dir1

ls -al
```

- **-p 옵션을 주면서 하위폴더들을 /로 연결하여 줄줄이 생성할 수 있다.**

```sh
mkdir -p dir2/subdir1/subdir2

ls dir2
subdir1

ls dir2/subdir1
subdir2
```

## 파일관리 cp/mv/rm

- 3가지 다 powershell에서 사용가능

### cp [파일] [원하는 경로/]or[새파일명]

- copy의 약자

### mv [파일] [원하는 경로/]or[새파일명] (파일이동/이름변경)

- **이름바꾸는 것도 mv로 하는구나.**

  ```sh
  mv new_file2.txt file2.txt
  ll
  ```

### rm [삭제파일명] / rm -r [디렉토리명]

- `-r` 이라는 recursive옵션을 주면 **디렉토리삭제+하위경로도 삭제** 2가지 역할을 해주게 된다.
  - 파일삭제는 rm 파일명
  - 디렉토리삭제는 rm -r 디렉토리명

```sh
rm new_file4.txt

rm -r dir3
```

#### rm [디렉토리] 후 선택 / rm [디렉토리] -Recurse

- 이부분 만 shell과 다름.
- 그냥 rm하면 경고창(wsl)없이 선택화면이 나온다. 디렉토리를 선택하면됨.
- 디렉토리 빠르게 삭제는 -r 옵션을 중간에 주는게 아니라 마지막에 -Recurse
  - powershell에서도 마지막에 -r 줘도 삭제되는 듯 싶다.

## Grep (select-string)

- Global regular expression print 의약자.

- 파일에서 검색 or `*`를 활용한 프로젝트 전체폴더에서 검색

### grep "검색문자열" [파일명]

```sh
grep "line" *.txt

file2.txt:second line
new_file1.txt:first line
new_file3.txt:third line---
new_file3.txt:third line-----
```

### grep -n : 라인정보 같이출력

- 찾은 것이 몇번째 라인에 있는지까지도 같이 출력

```sh
 grep -n "line" *.txt
file2.txt:1:second line
new_file1.txt:1:first line
new_file3.txt:1:third line---
new_file3.txt:2:third line-----
```

### grep -ni : 대소문자상관없이 검색후 라인정보출력

- `-i` : intensive 옵션을 주면 대소문자 상관없이 검색하라는 말이다.

```sh
 grep -ni "LINE" *.txt
file2.txt:1:second line
new_file1.txt:1:first line
new_file3.txt:1:third line---
new_file3.txt:2:third line-----
```

### grep -nir "문자열" . : 프로젝트최상경로에서 하위끝까지 전체를 검색

```sh
grep -nir "LINE" .

./file2.txt:1:second line
./new_file1.txt:1:first line
./new_file3.txt:1:third line---
./new_file3.txt:2:third line-----
```

### select-string [파일명] -pattern "검색문자열"

- default로 대소문자상관없이 + 라인정보도 같이 출력된다.

  ```powershell
  select-string *.txt -pattern "LINE"

  file2.txt:1:second line
  new_file1.txt:1:first line
  new_file3.txt:1:third line---
  new_file3.txt:2:third line-----
  ```

### select-string [*, \*/\*] -pattern "검색문자열"

- -ni는 자동이고, -r 옵션은 없어서 직접 `*`와 `,`를 연결한 경로를 지정(**경로패턴**)해줘야 전체 프로젝트 검색이 된다.

```powershell
select-string *, */* -pattern "LINE"

file2.txt:1:second line
new_file1.txt:1:first line
new_file3.txt:1:third line---
new_file3.txt:2:third line-----
```

- 대소문자를 구분하고 싶다면, 맨 뒤에 `-CaseSensitive`옵션을 주면 된다.

## 환경변수 설정

- 모바일, 웹 개발시 환경변수를 설정함
- 내 컴퓨터에서 특정한 키워드가 어떤 일을 하거나 경로를 저장할 수 있게 만드는 것.

### env

- 지정한 환경변수 목록을 본다. 사용시에는 앞에 `$`를 붙인다.

  ```sh
  env
  ```

#### ls env:

- powershell에서 보는 환경변수목록. 사용시에는 `$env:`를 앞에 붙인다.

  ```powershell
  ls env:
  ```

### export [대문자_변수명]="경로or문자열" (환경변수 설정)

- 대문자를 사용하고 사이에는 \_를 이용하여 구분한다.

  ```sh
  mkdir dir1
  env # 모든 환경 변수 확인

  export MY_DIR="dir1"
  ```

#### $env:[환경변수명] = "경로or문자열" for powershell

```powershell
$env:MY_DIR = "dir1"
```

### cd $환경변수명 (환경변수 사용)

- 환경변수를 터미널 등에서 사용할 수 있다.

  ```sh
  cd $MY_DIR

  pwd
  ```

#### cd $env:[환경변수명] for powershell

### unset 환경변수명 (환경변수 삭제)

```sh
unset MY_DIR

env
```

#### $env:[환경변수명] = ""

- powershell에서 환경변수 삭제는 빈 문자열을 넣어서 없애준다.

  ```powershell
  $env:MY_DIR = ""

  ls env:
  ```
