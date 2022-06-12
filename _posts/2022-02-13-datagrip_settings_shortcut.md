---
toc: true
layout: post
title: datagrip 세팅 및 단축키
description: datagrip vscode keymap기준 정리

categories: [datagrip, settings, shortcut]
image: "images/posts/config.png"
---


datagrip

### 설정

1. intellij settings export
2.  vscode keymap 설치
3. intellij settings import 
4. 설정 > Database > Query Execution > shortcut assign > `ctrl + enter` 
5. 설정 > Database > Query Execution > When inside statement execute : `ask -> smallest`
	![20220607100110](https://raw.githubusercontent.com/is2js/screenshots/main/20220607100110.png)



### 단축키
- `ctrl + shift + Q`: 새 쿼리 콘솔 열기
- `F5`(assign) : `execute` 단축키로 쿼리 실행하기
- `alt + 2` : file(tool window)
- `ctrl + shift + alt + F`: table의 where filter 포커싱
- `ctrl + shift + alt + G`(assing): 입력 가능한 live template 목록 보기
	- keymap -> `insert live template`검색 후 지정
	![20220607165914](https://raw.githubusercontent.com/is2js/screenshots/main/20220607165914.png)

- `ctrl + F6`: table선택상태에서 `테이블 수정`(alter table)

- query console의 `In-Editor Result`버튼
	- 수동 클릭으로 X 버튼 눌러줘야해서 불편하기도 함.
	![20220609160637](https://raw.githubusercontent.com/is2js/screenshots/main/20220609160637.png)
	![20220609160721](https://raw.githubusercontent.com/is2js/screenshots/main/20220609160721.png)



- `alt+8`: `service` 탭에 query console(*.sql) - session 연결을 확인할 수 있다.
	- alter table 하고 싶은데, sesion이 연결되어있을 경우 작동안한다.
	- `alt+8` -> 각 콘센트를 `Close Session`해주면 된다.
	![20220610175645](https://raw.githubusercontent.com/is2js/screenshots/main/20220610175645.png)

- `Ctrl+Alt+Shift + F4`: keymap `Close Session`검색후 단축키 지정해주기
	- `Close Session`: c + a + s + `F4`
		- sql파일 내에서 누르면 session 끊기고 + sql파일도 닫힌다.
	- `Close Session`: c + a + s + `F4` + `F4`
	![20220610175954](https://raw.githubusercontent.com/is2js/screenshots/main/20220610175954.png)
	
- **세션 확인 후 종료 : `alt+8`(service) -> `c+a+s+F4`(Close session)**

- `ctrl + ENTER`: default `Start New Line` 단축키
	- **자동완성을 피하면서 줄바꿈 하기 위함**
	- shift를 넣어주면 `Start New Line Before Current` 단축키로, 직전행에 새줄 생성 및 커서 이동

- `ctrl + shift + T`(assign): 조회 결과를 `transponse`해서 보기
	- keymap > `transpose`검색 > `plugins`하위 > c+s+T로 지정하기
	![20220611234915](https://raw.githubusercontent.com/is2js/screenshots/main/20220611234915.png)
