---
toc: true
layout: post
title: vscode markdown link shortcut by kinbinding
description: keybindings.json에 Ctrl+K로 clipboard url link 걸기
categories: [configuration, vscode]

image: "images/posts/config.png"

---

### keybindings.json 열어 아래 코드 복붙하기
1. F1 > `keyboard` 검색후 > `바로가기 키 열기 (JSON)`을 선택한다.
	![20220518161423](https://raw.githubusercontent.com/is2js/screenshots/main/20220518161423.png)

2. 아래 코드를 맨 위에 붙여넣는다.
	```json
		// 스니펫 참고: https://code.visualstudio.com/docs/editor/userdefinedsnippets
		// 마크다운일 때, 링크 삽입 태그 추가
		{
			"key": "ctrl+k",
			"command": "editor.action.insertSnippet",
			"args": {
				"snippet": "[${TM_SELECTED_TEXT}](${CLIPBOARD})"
			},
			"when": "editorHasSelection && editorLangId == 'markdown'"
		},
	```

3. 이후 `clipboard에 url을 복사한 상태`에서, markdown 내 텍스트에 <kbd>ctrl + k</kbd>를 눌러서 링크를 바로 건다