---
toc: true
layout: post
description: github의 인덱스와, gitblog의 index.html을 꾸미는 법 공부하기
categories: [git, github]
title: 프로필) github index, badge 등 생성하여 꾸미기

image: "images/posts/git.png"

---

## 참고사이트

- 참고 사이트
  - https://velog.io/@woo0_hooo/Github-github-profile-%EA%B0%84%EC%A7%80%EB%82%98%EA%B2%8C-%EA%BE%B8%EB%AF%B8%EA%B8%B0
  - https://butter-shower.tistory.com/142
  - https://velog.io/@colorful-stars/Github-%ED%94%84%EB%A1%9C%ED%95%84-%EA%BE%B8%EB%AF%B8%EA%B8%B0
  - http://blog.cowkite.com/blog/2102241544/

## github_id 이름으로 repo 생성

1. github ID와 동일한 이름으로 repo만들기 + add Readme

   - local에 clone하여 환경 세팅하자.
     `C:/jupyter_blog/` + `is2js`

     ![image-20210819202218848](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210819202218848.png)

## capsule-render로 header/footer 작성

1. typora에서 미리보기를 활용하여 header footer를 작성한다.

   - 각 옵션 목록 : https://github.com/kyechan99/capsule-render

   - default(변경하면됨)

     ```markdown
     ![header](https://capsule-render.vercel.app/api?type=waving&color=f6ebe1&height=150&section=header&text=Data Engineer and KMD&fontSize=50&fontColor=152447&desc=데이터 엔지니어를 꿈꾸는 한의사, 조재성입니다.&descAlignY=80)

     ![footer](https://capsule-render.vercel.app/api?type=rect&color=152447&height=20&section=footer)
     ```

     ![image-20210819204503883](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210819204503883.png)

## sheilds.io로 기술 스택 badge작성

shields.io를 통해 기술스택, 연락처 등의 스택아이콘 가져온다.

- `a태그 > img태그 `

- a태그는 링크주소를 / img는 badge를 여기를 통해서 편하게 가져온다.

  - 각 뱃지 목록 : https://github.com/alexandresanlim/Badges4-README.md-Profile

  ```markdown
  <p align="center">
      <a href="mailto:tingstyle1@gmail.com"><img src="https://img.shields.io/badge/Gmail-d14836?style=flat-square&logo=Gmail&logoColor=white&link=tingstyle1@gmail.com"/></a>&nbsp
      <a href="https://www.facebook.com/tingstyle1"><img src="https://img.shields.io/badge/Facebook-1877F2?style=flat-square&logo=facebook&logoColor=white"/></a>&nbsp
      <a href="https://www.github.com/is2js"><img src="https://img.shields.io/badge/GitHub-100000?style=flat-square&logo=github&logoColor=white"/></a>&nbsp 
  </p>
  ```

## productive-box로 커밋시각 통계(gist) 노출(pinned)하기

- 참고 : http://blog.cowkite.com/blog/2102241544/

1. [gist.github.com](https://gist.github.com/)에 public으로 신규 **`public`으로 바꾼 gist를** 생성
   ![image-20210820200806703](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210820200806703.png)

   ![image-20210820200610811](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210820200610811.png)

2. [github토큰 생성 페이지](https://github.com/settings/tokens/new)로 가서 `repo`, `gist`를 포함한 scope를 설정한 뒤, 발급받은 토큰을 잘 챙겨둔다.

   ```
   QQQghp_ysd0aOZbgsOJ3rOTAhj4QQQw4su6rrfPp1oc7WrQQQ
   ```

3. [productive-box](https://github.com/maxam2017/productive-box) repository를 fork한다.

   - 우측 상단의 `Fork` 버튼을 누르면 된다.

   - fork 된 나의 repository의 **Actions** 탭에서 `enabled` 버튼을 눌러 Action을 활성화한다

   - ```plaintext
     .github/workflow/Schedule.yml
     ```

     파일을 수정하여 **환경변수를 작성**해준다

     - **uses**: **is2js**/productive-box@master : 내github아이디로 바꿔준다?

       - maxam2017 -> 내 아이디로

     - **GIST_ID**: *사전 작업의 1번 step*에서 생성된 gist의 id (gist URL은 `gist.github.com//`로 생성되기 때문에 주소창을 보면 된다.)
       - `042e89789d6054e9b372e755e75f09f5`
     - **TIMEZONE**: 타임존을 적어준다. `Asia/Seoul` 형식으로 적어주면 된다.
       - cf) cron : 분시일월주
         - cron예제 : https://ponyozzang.tistory.com/402

4. 해당레포 > Settings 탭 > Secrets에 접속한 뒤 New repository secret 버튼을 클릭하여 환경변수를 설정해준다.

   - **GH_TOKEN**: *사전 작업의 2번 step*에서 발급받은 토큰

     - 토큰은 github > secrets > 환경변수에서 설정해주면, workflows.config.yaml을 그것을 이용한다.

     ```yaml
     env:
       GH_TOKEN: ${{ secrets.GH_TOKEN }}
     ```

   ![image-20210820201930495](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210820201930495.png)

5. gist를 내 프로필 pinn에 고정시킨다.
   ![image-20210820202055897](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210820202055897.png)![image-20210820202646694](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210820202646694.png)

6. 내 블로그에 embed시킨다.

   - 아래 코드에서 gist주소만 복사해서 `gist_id.js`를 넣어주면 됨.

   <script src="[gist주소].js"></script>

## 깃허브 프로필 뱃지 얻기

### Github Developer program

1. https://developer.github.com/program/ 사이트에서 등록한다.

   - 2가지만 추가로 채워주면 된다.

   - Highlights부분에 뱃지 생김

     ![image-20210822202248713](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210822202248713.png)

### 학생 PRO

- 유효한 date를 포함하는, 학생증 찍어서 증명하는 부분이 생겨 나는 포기함.

1.  [https://education.github.com](https://education.github.com/) 접속후 Student Developer Pack 클릭

1.  Get pack > Get Student benefits > 아래쪽 학교인증

    - github에 학교계정으로 email을 등록하고 와야한다.

      ![image-20210820205641913](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210820205641913.png)

1.  마지막에 plan을 `Github Pro`로 적어주면 된다.

1.  증명서를 내라고한다. 나는.. 졸업해서 그냥 생략..

### 북극곰 2020.02월

- 나는 우연히 그 때 만들어뒀나보다.
  ![image-20210820204453873](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210820204453873.png)
