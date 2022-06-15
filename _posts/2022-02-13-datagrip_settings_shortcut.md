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
	- 참고로 왼쪽 console에 쿼리실행시간도 나온다.
		![20220612215830](https://raw.githubusercontent.com/is2js/screenshots/main/20220612215830.png)

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


- query문 마다 **바로 위 `--commnet`가 result tab의 제목이 된다.**
	- **comment까지 같이 포함시켜 실행해야하며, 여러 쿼리문 실행시 나눠서 보여준다.**
	![b3880605-aec4-41db-966e-382f6c843cfc](https://raw.githubusercontent.com/is2js/screenshots/main/b3880605-aec4-41db-966e-382f6c843cfc.gif)

- result tab에 있는 `Compare with` 버튼으로 result tab끼리 비교도 할 수 있다.
	![20220612215654](https://raw.githubusercontent.com/is2js/screenshots/main/20220612215654.png)
	![20220612215704](https://raw.githubusercontent.com/is2js/screenshots/main/20220612215704.png)


- **`F4` + table: table에 담긴 데이터를 바로 볼 수 있는 `data editor`가 열린다.**
	- csv, tsv 등 데이터를 편하게 복붙해도 알아서 인식해서 넣을 수 있음.
	![20220612220029](https://raw.githubusercontent.com/is2js/screenshots/main/20220612220029.png)
	- `F4` 상태에서 데이터를 변경하면
		1. submit전에 해당 셀을 `revert`되돌리기 할 수 있다.
			- **변경이 감지된 셀을 클릭한 상태에서 가능하다. 단축키는 `ctrl + alt + Z`**
		2. submit전에 DML을 `눈알버튼`으로 확인할 수 있다.
			![004febe0-aad1-4af6-96df-907abec39184](https://raw.githubusercontent.com/is2js/screenshots/main/004febe0-aad1-4af6-96df-907abec39184.gif)
	- `F4` 상태에서 **`fk(파란색열쇠)칼럼`에 대해서 `F12`를 통해 관련된 `pk테이블 row`로 이동한다**
		![0e4e00d6-6de9-495d-b970-08cf0a2e0f17](https://raw.githubusercontent.com/is2js/screenshots/main/0e4e00d6-6de9-495d-b970-08cf0a2e0f17.gif)
	- `F4` 상태에서 `F12`를 통한 pk row로 이동했다면, filter에 해당 pk가 필터링 되어있는데
		- **`c+s+a+ F`를 통해 FILTER(WHERE)에 옵션을 추가해서 확인할 수 있다.**
			![a196acb9-1001-41a5-9886-719dfe7ab21d](https://raw.githubusercontent.com/is2js/screenshots/main/a196acb9-1001-41a5-9886-719dfe7ab21d.gif)
		- 일반 `ctrl+F`를 통해 데이터를 검색할 수 있으며, 정규표현식 / 찾은 데이터만 보기 / 검색 범위 넓히기 등이 가능하다.
			![56f03687-01d8-455f-8a12-e5503aecc5b1](https://raw.githubusercontent.com/is2js/screenshots/main/56f03687-01d8-455f-8a12-e5503aecc5b1.gif)

- **`alt + F12`: table DDL을 팝업으로 띄운다.**
	![20220612220137](https://raw.githubusercontent.com/is2js/screenshots/main/20220612220137.png)

- `ctrl + LMB`: table DDL로 간다.
	- `F12`: table DDL로 간다. F4-fk칼럼에서 선택시 pk-row로 이동한다