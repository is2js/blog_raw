---
toc: true
layout: post
title: method refactoring
description: method관련된 리팩토링 살펴보기

categories: [java, 우테코]
image: "images/posts/wootech.png"
---

### 메소드관련 리팩토링





![image-20220208161557404](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208161557404.png)



![image-20220208161641871](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208161641871.png)



#### 01 메서드 추출로 [for indent] 제거

![image-20220208161751819](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208161751819.png)





- indent는 메서드 분리로 해결한다.

    ![image-20220208161833152](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208161833152.png)

    

#### 02 early return으로 [else] 제거

- else 제거 by early return

    ![image-20220208161947277](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208161947277.png)

![image-20220208162012050](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208162012050.png)



#### 03 메서드 분리 -> [일을 1가지씩 순차적으로]

- 1가지 일만하도록 메서드 분리
    - convert(문자열[]-> 숫자[] )  +  누적합sum

![image-20220208162043109](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208162043109.png)

![image-20220208162117516](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208162117516.png)



- 보기 좋음 >>> 약간의 성능저하



#### 04 로컬변수 제거 -> [ line 줄이기]



![image-20220208162301276](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208162301276.png)

![image-20220208162304774](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208162304774.png)





#### 05 compose method 패턴 -> [조건문 보기 좋게]

- 조건문에 적용: https://wooyaggo.tistory.com/34
- 심화: https://mygumi.tistory.com/343

![image-20220208162327440](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208162327440.png)

- **`조건문`을 의도를 가진 메서드로** 바꾼다 -> 파라미터의 type이나 조건이 **바뀔 때 `사용된 조건문`을 일괄해서 바꿀 수 있다.**

![image-20220208162332034](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208162332034.png)



- 읽기좋게 레벨 맞추기

    ![image-20220208162400185](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208162400185.png)









- 비교


    ![image-20220208162412464](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220208162412464.png)

