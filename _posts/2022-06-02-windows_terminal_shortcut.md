---
toc: true
layout: post
title: window terminal 단축키 매핑
description: windows terminal 단축키를 vscode와 비슷하게 매핑
categories: [configuration, windowsterminal, shortcut]

image: "images/posts/config.png"
---


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
