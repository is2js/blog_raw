---
toc: true
layout: post
categories: [rdp, iptime, 원격, configuration]

title: 데스크탑 iptime RDP 설정
description:  chodesktop.iptime.org:4444

image: "images/posts/config.png"

---

### iptime에 물린 컴퓨터/테블릿 중 원격접속할 [데스크탑 내부ip]를 확인한다.
1. 192.168.0.1  > 관리도구

2. 고급 설정 > 네트워크 관리 > 내부 네트워크 정보

3. 원격접속할 데스크탑의 내부ip 확인

    ```
    192.168.0.11	D8-BB-C1-5F-99-3A	DESKTOP-CJROKE9	유선:자동할당	
    ```





### 데스크탑 내부ip를 포트포워딩 설정(외부:4444 1~100빼고 맘대로, 내부:3389 rdp고정)
- 고급 설정 > NAT/라우터 관리 > 포트포워딩 설정
    - 외부포트는 1~100를 제외한 맘대로(4444)로 설정해준다.
	- 내부포트는 3389가 고정이어야한다(3389로 보내야, RDP로서 컴퓨터가 port 오픈해줌)
    

![image-20220419003336077](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220419003336077.png)

![image-20220419003535599](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220419003535599.png)







### 기본 설정 > 시스템 요약정보에서 [외부IP]확인 or DDNS 사용하기

- 외부IP는 동적IP이기도 해서 외부IP는 사용하지 않는다. 
- IP대신 DDNS를 제공받아서 `chodesktop.iptime.org` 자체를 내 외부ip로 사용한다.







### 원격 제어되는 컴퓨터 설정(원격 설정 활성화/계정 비번설정/계정 확인) 

1. 시작메뉴에서 `원격 설정` 검색 > 원격 데스크톱 설정 > [x] 원격 데스크톱 활성화 
2. 현재 사용자 비밀번호가 없다면 설정
	- win + i > 계정 > 로그인 옵션 > 비밀번호 > 추가
		![image-20220419010715507](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220419010715507.png)
3. 시작메뉴에서 `control userpasswords2`로 사용자 이름 확인







### mstsc > chodesktop.iptime.org:4444 > cho_desktop로 RDP 접속하기