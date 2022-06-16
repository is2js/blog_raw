---
toc: true
layout: post
title: 생소한 윈도우 로고 단축키 모음
description: windows 로고 단축키 생소한 것들 모음
categories: [windows, shortcut]

image: "images/posts/config.png"

---

### 활용
- <kbd>ctr + shift + win + {Num}</kbd> : 작업표시줄에 올려진 프로그램을 관리자 권한으로 실행
- <kbd>alt + win + {Num}</kbd> : 작업표시줄에 올려진 프로그램에 우클릭 역활(최근 파일, 고정 파일 보기)

- <kbd>win + m</kbd> -> <kbd>win + shift + m</kbd> : 현재창 최소화 -> (작업) -> 최소화 한 것 복구
- <kbd>win + home</kbd> -> <kbd>win + home</kbd> : 현재창을 제외한 다른 창들 최소화 -> (작업) -> 최소화 한 것 복구
- <kbd>win + ,</kbd> : 일시적으로 바탕화면 보기


- <kbd>win + ↓</kbd> : 현재 창 최소화
- <kbd>win + shift + ↑</kbd>: 세로 방향으로만 윈도우에 붙여 최대화
- <kbd>win + shift + ↓</kbd> : 윈도우에 붙은 상태에서 `붙기 전 모습 작은 창`으로 복구

- <kbd>win + shift + ←, →</kbd>: 윈도우 붙는 단위가 아닌 모니터별 창 이동시키기



### 설정 관련
- <kbd>win + a</kbd> : 윈도우 액션 설정 창(블루투스, 핫스팍 관련)

- <kbd>win + i</kbd> : 윈도우 설정 창
	![20220602155148](https://raw.githubusercontent.com/is2js/screenshots/main/20220602155148.png)
	- <kbd>win + u</kbd> : 접근성 설정 창


- <kbd>win + k</kbd> : 연결 장치(bluetooth) 검색

- <kbd>win + s</kbd> : 작업표시줄 검색창 

- <kbd>win + b + → → enter</kbd> : 인터넷 액세스 상태 보기

### 부가 기능

- <kbd>win + alt + d</kbd> : 달력(다이어리) 열기

### 실행 명령어
- [실행 명령어](https://ryuseunghyun.tistory.com/973)
- [실행 폴더](https://goaway007.tistory.com/entry/%EC%9C%88%EB%8F%84%EC%9A%B0-10-%EB%94%94%EB%A0%89%ED%86%A0%EB%A6%AC-%EA%B2%BD%EB%A1%9C-%EB%AA%85%EB%A0%B9%EC%96%B4%EC%99%80-%ED%95%B4%EB%8B%B9-%EC%9C%84%EC%B9%98-%EC%A0%95%EB%A6%AC-%EB%AA%A8%EC%9D%8C)

#### 설정
- `firewall.cpl`: 방화벽 설정 열기
- `sysdm.cpl`: 시스템 속성 열기
	- 고급 > **환경변수 설정**
- `appwisz.cpl`: 프로그램 등록/제거


- `mstsc`: 원격 데스크톱 열기
- `winver`: 윈도우 버전 + **컴퓨터 계정명**확인하기
- `control userpasswords2` : 사용자 계정


#### 폴더

- `%APPDATA%`: 설정 폴더들
- **`%homepath%`** = `%path%` =  `%userprofile%`: 사용자명 폴더(C:\Users\{username})
	- 유저명 얻기시에만 `%homepath%`로 통일하자.
		- path는 탐색기 경로(ctrl+L)에서 안먹힌다.
		- username은 실행(ctrl+R)에서 안먹힌다.
		- **homepath나 userprofile은 둘다 먹힌다.**
	- `/desktop` : 바탕화면 
	- `/downloads` : 다운로드 폴더
	- `/anaconda3` : 아나콘다 폴더 가기
- `%PROGRAMFILES(X86)%` or `%PROGRAMFILES%`: 프로그램 폴더



#### 프로그래밍
- `gcm 프로그램명`: 프로그램(.exe?) 위치 찾기
	- `Get-Command 프로그래명`과 동일