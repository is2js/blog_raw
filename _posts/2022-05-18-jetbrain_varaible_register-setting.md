---
toc: true
layout: post
title: jetbrain(intellij, pycharm) bin register in variable
description: idea .과 pycharm .으로 IDE 실행하도록 bin폴더를 환경변수에 넣어주기
categories: [configuration, intellij, pycharm, cmd, terminal, bin, 환경변수]

image: "images/posts/config.png"

---

### toolbox로 설치한 제품 위치
- pycharm : `C:\Users\is2js\AppData\Local\JetBrains\Toolbox\apps\PyCharm-P\ch-0\`
- intellij : `C:\Users\is2js\AppData\Local\JetBrains\Toolbox\apps\IDEA-U\ch-0`
	- **버전 업데이트마다 `bin\`폴더까지를 환경변수에 넣어준다.**
		- intellij: `idea`
		- pycharm: `pycharm`

### intellij bin 폴더를 Path에 등록하여 `idea .`으로 실행
1. 탐색기 > `내컴퓨터`우클릭 > `속성` 선택
	- <kbd>win + R</kbd> -> <kbd>sysdm.cpl</kbd>

2. `환경변수` 클릭

3. `사용자 변수` > `Path`(기본으로 인식될 경로 모음) 선택
	![20220518123532](https://raw.githubusercontent.com/is2js/screenshots/main/20220518123532.png)

4. `새로 만들기` 선택 후 `intellij의 bin폴더`까지의 경로 입력
	- intellij의 파일위치 열기 -> bin 내부 폴더임
		![![20220518123343](httpsraw.githubusercontent.comis2jsscreenshotsmain20220518123343.png)](https://raw.githubusercontent.com/is2js/screenshots/main/![20220518123343](httpsraw.githubusercontent.comis2jsscreenshotsmain20220518123343.png).png)
		```
		C:\Users\cho_desktop\AppData\Local\JetBrains\Toolbox\apps\IDEA-U\ch-0\221.5591.52\bin
		```
5. 프로젝트 폴더 경로에서 `idea .`으로 실행
	- `.` 빠지면 기존 실행중이던 것만 깜빡이는 것 같다. 항상 붙여주자.
	![20220518123907](https://raw.githubusercontent.com/is2js/screenshots/main/20220518123907.png)


### pycharm도 bin 등록하여 `pycharm .`으로 실행
1. Path에 pycharm의 bin경로를 걸어준다.
	```
	C:\Users\cho_desktop\AppData\Local\JetBrains\Toolbox\apps\PyCharm-P\ch-0\221.5591.52\bin
	```

2. 터미널에서 `pycharm .`명령어로 실행한다.