---
toc: true
layout: post
title: command패턴 중 cmd객체 외부저장소List에 cursor도입 정리
description: command패턴에서 사용된 redo를 위한 cursor

categories: [java, 우테코, oop, object, menu, command, memento, 정리 ]
image: "images/posts/java.png"
---

### command홀더객체 내, cmd객체들 외부저장소(List배열)과 cursor로 상태확인

![image-20220813133731095](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813133731095.png)



1. redo는 undo로 인해 cursor가 원래배열의 끝보다 더 뒤에 있을 때, 한칸 앞으로 움직이므로

   - **cursor가 `배열의 맨끝`에 있지는 않은지** 검사한다

     ![image-20220813132600229](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813132600229.png)

2. undo는 실행되어 기록이 저장된cmd가 1개라도 있어야한다. 

   - **cursor가 `원소가 1개도 없을 경우인,  0보다 뒤에 있는지 확인`**한다

     ![image-20220813132731535](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813132731535.png)

3. execute후 add는, **현재 add된 것이 배열의 제일 마지막에 위치**하도록 **(add를 단순 cursor++만 해선 안됨)** 해야 **add후 redo시 에러가 발생안하게** 되는데, cursor는 undo로 인해 배열 끝보다 더 뒤에 있을 수 있으니, **add하는 순간에는 cursor이동 전에, 배열 remove로직도 들어간다**.

   - **cursor만 빽(undo)하고 redo를 위해 배열에는 cmd객체를 남겨놓는데, `cursor가 빽 한 상태에서 add한 것이 배열의 제일 끝에 위치하여, redo는 불가능`하도록 만들어놔야한다 ** 

   - **그럴려면, `현재cursor(빽한 상황)보다 더 앞에 있는 cursor +  1 ~ 마지막까지는 먼저 배열에서 삭제`해놓아야 한다**

   - 그리고 난 뒤, **`요소를 배열 맨마지막에 add했으면, cursor의 업데이트는 불변식 size()-1로 업데이트`**한다

     ![image-20220813133653313](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220813133653313.png)