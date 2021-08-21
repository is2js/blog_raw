---
toc: true
layout: post
description: fastpages를 이용하여 notebook, md파일로 쉽게 블로그를 관리할 수 있도록 해봅시다.
categories: [fastpages, blog]
title: fastpages를 이용한 블로그 제작
---

## 1 fastpages 만들기

- **클릭만으로 repo, actions key 생성/ 추가 merge**

1. google에서 `fastpages`검색후 > 깃허브 > setup instruction > on this link (generating by link)

   - is2js(~~@naver.com~~) / 12!@

2. 블로그 원본 내용이 들어갈 레포지토리 생성임.(~~github.io 아님~~)

   - is2js/**`blog_raw`**
     - 이후 자동으로 `https://is2js.github.io/blog_raw/` 로 생성된다.

3. 조금 기다리면, Pull Request가 자동으로 올라온다.

   - link클릭으로 create/clone repo + PR으로 설치까지 자동화된 것임.

   - **PR로 온 Initial Setup**부분을 보자. merge전에 1,2,3번은 순서대로 직접 작동시켜야한다.

     - `1.` ctrl+클릭으로 this uility를 클릭 > RSA - 4096 클릭 > 생성된 key 그대로 복사

       Private Key

       ```
       -----BEGIN RSA PRIVATE KEY-----


       ```

       Publick key

       ```
       ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCGIVY8FQf9x1Y52HUUJmpAyOfrzH96K4QtGb7

       ```

     - `2.`도 컨트롤+클릭해서 `1.`에서 생성한 ssh key를 붙혀넣어 **Action secret key를 만든다.**

       - actions secrets 부분에 `New repository secret`을 클릭해서 붙혀넣음.
         - Name : **`SSH_DEPLOY_KEY`**
         - Value : `SSH RSA 4096 private key 복붙`

     - `3.` 도 컨트롤+클릭해서 **fastpages-blog**의 **Deploy (public) key**를 생성해야한다. `Add deploy key`클릭

       - 여기서는 생성한 ssh RSA 4096 key중에 밑에 있는 publick key를 사용한다.

         - Title : **`fastpages-blog`**
         - Key : `SSH RSA 4096 public key 복붙`
         - **[O]** Allow write acess

4. PR 아래부분에 merge PR > merge를 눌러준다.
   - Actions에 보면 Merge가 진행중인 것이 보인다. 2분 정도 걸림.
   - 다 되고나면, `<code>`탭으로 와서 맨 첫줄에 블로그 주소가 보이게 된다.

## 2 설정해주기

### blog 타이틀 변경

깃헙을 다룰 줄 알면, 로컬에서 clone을 떠서 수정하면 된다.

- push만 해주면, actions가 자동으로 돌아가면서 static 파일로 수정해준다.
- 우리가 올린 파일 기준으로 static한 파일을 생성해준다.

1. \_config.yaml 수정 > title 변경 > commit

### jupyter upload

1. fastpages가 만들어준 readme에 보면, `writing blogs with jupyter`가 있다.

   - 여기서는 .md or .ipynb의 파일이름 양식만 참고함.

     - YYYY-MM-DD-\*.ipynb

     ```
     2020-01-28-My-First-Post.ipynb
     2012-09-12-how-to-write-a-blog.ipynb
     ```

   - Front Matter : 내 블로그 첨에 작성된 ipynb글(`Fastpages Notebook Blog Post`)을 참고해서 가져다 쓴다.(공식은 너무 김)

     - 쥬피터 노트북 맨 위에 들어갈 markdown 양식이다.

       ```
       # "My Title"
       > "Awesome summary"

       - toc:true
       - branch: master
       - badges: true
       - comments: true
       - author: Hamel Husain & Jeremy Howard
       - categories: [fastpages, jupyter]
       ```

     - 올릴 쥬피터에 아래와 같이 작성한다.
       ![image-20210806205543412](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210806205543412.png)

     - 노트북 이름을 바꾼다.

       ```
       2021-08-06-엑셀정리 05 캐글 1 타이타닉 - 변수별 EDA + 함수정의로 정의(upload).ipynb
       ```

     - github의 `_notebooks` 폴더로 가서 add file > 업로드 > commit해보았음.

### markdown upload

- 해당 폴더는 `_posts`다

- 제목양식은 `yyyy-mm-dd-제목.md`다

  - **실제로 표시되는 이름은 예제파일속 format의 `title:`**이 실제로 포스트 제목이 된다.
  - \_posts > 예제파일.md > raw를 클릭해서 내용을 복사후 > add file > create file

  ```
  ---
  toc: true
  layout: post
  description: A minimal example of using markdown with fastpages.
  categories: [markdown]
  title: An Example Markdown Post
  ---
  # Example Markdown Post

  ## Basic setup
  ```

## 3. local에서 clone해와서 작업해보기

1. `C:\jupyter_blog`에 폴더를 만들고, 우클릭 > 터미널 > `code .`

2. `git clone https://github.com/is2js/blog_raw.git`

   - C:\jupyter_blog\blog_raw에 파일 복사됨.

   - vscode는 해당 폴더로 들어가야함.

     ```shell
     cd blog_raw/
     ```

3. ~~블로그 계정(is2js)과, 컴퓨터 전체 git 계정(is3js)이 다르므로, `--local`로 깃 계정을 설정해준다.~~

   - [참고](https://awesometic.tistory.com/128)

   ```shell
   git config --local user.name "is2js"
   git config --local user.email "is2js@naver.com"
   ```

   - vscode에서 github확장관리에서 로그인을 제공하고 있어서, 로그아웃후 is2js로 로그인했다.

### 첫화면 수정

1. index.html은 첫 화면에 대한 내용을 md형식으로 닮고 있다.
2. 내용을 수정하고 이미지가 필요하면, images폴더에 업로드후 사용한다.
   - **작성글들은 이 페이지 아래 자동으로 모아진다.**

### 메뉴 추가

![image-20210816204719304](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210816204719304.png)

1. `_pages/`폴더에 들가면 각각의 메뉴가 md파일로 작성되어있다.

   - about은 `md`파일로 작성되어있으며, **추가 nav 메뉴는 다 md파일로 작성한다.**

     ```
     ---
     layout: page
     title: About Me
     permalink: /about/
     ---
     ```

     - ~~파일명이 아니라~~ **양식속 title**이 nav메뉴에 뜨는 페이지명이 된다.

   - 404, search, tags는 `html`파일로 섞어서 작성되어있다.

2. `_pages/`에서 파일을 하나 생성하여 메뉴를 추가한다.
   - about에 있는 양식을 사용한다.
   - 작성하고 commit해준다.

### comment 추가

- utterances를 이용하여 깃허브로 댓글 달도록 함.

1. 현재 아무 post에나 comment를 달면 `is2js/blog_raw/`폴더에 앱이 인스톨 안되어있다고 나옴.
   - install_app을 클릭해서 어터런스 설치
   - **install을 all repo가 아니라 select해준다.**
     - 필요한 저장소마다 설치를 추가로 해주면 된다.

### social comment

- disqus를 이용하여 소셜 or 깃헙없이 댓글 달도록 함.
- utterances설치시 생성된 `_includes/utterances.html`을 수정하여 스크립트로 disqus스크립트를 넣어준다.
  - disqus는 url기준으로 댓글창을 열어준다.
  - **url만 다르다면, 다른 댓글창이 열린다.**

1. 구글에서 `disqus` 검색
   - 블로그 > 로고클릭 > com
2. 난 페북으로 로그인함. -> get start
3. 선택

- ~~i want to comment on sites~~
- `I want to install Disqus on my site`

4. site네임은 `is2js_blog_raw`로 함

5. basic버전을 subscribe now로 선택함

6. **우리는 `스크립트로 넣을 거라` platform선택 없이 아래 `i don't see my platform`** 선택

   - 스크립트는 카피 해놓는다.

     ```javascript
     <div id="disqus_thread"></div>
     <script>
         /**
         *  RECOMMENDED CONFIGURATION VARIABLES: EDIT AND UNCOMMENT THE SECTION BELOW TO INSERT DYNAMIC VALUES FROM YOUR PLATFORM OR CMS.
         *  LEARN WHY DEFINING THESE VARIABLES IS IMPORTANT: https://disqus.com/admin/universalcode/#configuration-variables    */
         /*
         var disqus_config = function () {
         this.page.url = PAGE_URL;  // Replace PAGE_URL with your page's canonical URL variable
         this.page.identifier = PAGE_IDENTIFIER; // Replace PAGE_IDENTIFIER with your page's unique identifier variable
         };
         */
         (function() { // DON'T EDIT BELOW THIS LINE
         var d = document, s = d.createElement('script');
         s.src = 'https://is2js-blog-raw.disqus.com/embed.js';
         s.setAttribute('data-timestamp', +new Date());
         (d.head || d.body).appendChild(s);
         })();
     </script>
     <noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
     ```

7. configure(next랑 똑같음) 클릭후 정보는 비워놓아도 된다.

## 4. 추가 커스텀

### 방문자 카운터

- 해당 프로젝트는 오픈소스라 작동이 안되거나 없어질 수 있음.
- 키워드를 기억했다가 다른 것을 검색해서

1. 구글에서 `github blog hits counter`를 검색

   - 최상단 `hit-counter` 깃허브 들어갔음.
     - https://hits.seeyoufarm.com/

2. 해당 내용을 채운다.

   ![image-20210816222306283](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210816222306283.png)

3. 3가지 타입으로 넣을 수 있다. 특히 embed로 넣을 수 있는 것은 노션으로도 넣을 수 있다는 말이 된다.

   - markdown

     ```
     ![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fis2js.github.io%2Fblog_raw&count_bg=%239E9E9E&title_bg=%2308445E&icon=python.svg&icon_color=%23E1DFDF&title=%EB%B0%A9%EB%AC%B8%EC%9E%90+%EC%88%98&edge_flat=false)
     ```

   - html

     ```
     <a href="https://hits.seeyoufarm.com"><img src="https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fis2js.github.io%2Fblog_raw&count_bg=%239E9E9E&title_bg=%2308445E&icon=python.svg&icon_color=%23E1DFDF&title=%EB%B0%A9%EB%AC%B8%EC%9E%90+%EC%88%98&edge_flat=false"/></a>
     ```

   - embed url

     ```
     https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fis2js.github.io%2Fblog_raw&count_bg=%239E9E9E&title_bg=%2308445E&icon=python.svg&icon_color=%23E1DFDF&title=%EB%B0%A9%EB%AC%B8%EC%9E%90+%EC%88%98&edge_flat=false
     ```

4. 이제 markdown을 index.html안에다 넣어주자.

## 5. 깃허브 잔디와 레포 페이지(git.html) 추가

- 잔디이미지는 솔루션을 통해
  - https://ghchart.rshah.org/{{site.github_username}}
- 레포정보는 url로 접근하는 github api를 통해
  - 레포들정보 : `https://api.github.com/users/[is2js]/repos`
  - 특정레포정보 : `[https://api.github.com/repos/[is2js]/[레포명]`
- [참고자료(노션)](https://www.notion.so/paullabworkspace/bea835c9e45946a2a5f88cd974a2d4d1)

1. 솔루션([https://ghchart.rshah.org](https://ghchart.rshah.org/) )을 이용하여, 잔디정보를 이미지로 가져올 수 있다.

   - index.html에 유저이름만 적어준 이미지 url or MD을 붙혀넣어준다.

     - 이미지 주소 : `https://ghchart.rshah.org/is2js`

     - 이미지태그

       ```
       <img src="https://ghchart.rshah.org/{{site.github_username}}"/>
       or
       <img src="https://ghchart.rshah.org/is2js" />로 변경
       ```

     - **마크다운 주소**

       **`![잔디](https://ghchart.rshah.org/is2js "https://github.com/is2js")`**

   - index.html의 hits 아래에 넣고 확인해보자.

     ![image-20210816234036732](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210816234036732.png)

2. 새로운 페이지 /\_pages/`git.html`을 만들자.

   - 노션에 미리 **작성해둔 코드를 복붙하자.**

     - js스크립트를 사용하기 위해 `html`양식으로 작성한다. **github API**를 **script태그를 이용하여 github repo정보를 가져온 것이**다.
       - api -> h2, p, a태그로 map으로 돌면서 정보출력. 원하면 태그를 바꾸면 된다.
     - `is2js`부분을 해당하는 githubID로 변경해서 사용하면된다.

     ```javascript
     ---
     layout: default
     permalink: /gitInfo/
     title: gitInfo
     search_exclude: true
     ---
     <img src="https://ghchart.rshah.org/is2js" alt="" style="width: 660px; height: 100px;">
     <div id="repos">
     <script>
     // 비동기 작업을 위해 async function 선언
     const initRepo = async () =>{
             const _username = 'is2js'

             // 추가할 dom선택
             const repoUl = document.getElementById("repos")

             // headers 설명 : 깃허브에 요청할 형식 지정
             let response = await fetch('https://api.github.com/users/is2js/repos',
             {headers:{
                 'Accept' : 'application/vnd.github.v3+json'
             }});
             const repoJson = await response.json()
             // repo받아온 내용 확인
             // console.log(repoJson);

             // json내용을 map을 이용해 각 dom을 만들고 추가
             repoJson.map((repos)=>{
                 let h2Element = document.createElement("h2")
                 h2Element.textContent = repos.name

                 let pElementDesc = document.createElement("p")
                 pElementDesc.textContent = "레포설명 : " + repos.description

                 let aElementURL = document.createElement("a")
                 aElementURL.href = repos.svn_url
                 aElementURL.textContent = repos.svn_url

                 //각 요소를 <div id="repos">에 추가
                 repoUl.append(h2Element)
                 repoUl.append(pElementDesc)
                 repoUl.append(aElementURL)
                 repoUl.append(document.createElement("br"))
             })
         }
         initRepo()
     </script>
     ```

## 6. repo마다 언어 퍼센트 출력하도록 변경

- 특정 레포 : `[https://api.github.com/repos/[is2js]/[레포명]`

  - 특정레포에서 쏴주는 정보를 관찰해보기 : [blog_raw](https://api.github.com/repos/is2js/blog_raw)

  - 랭기쥐만 출력해서 보기 : [blog_raw/languages](https://api.github.com/repos/is2js/blog_raw/languages)

    - 각 언어의 line수가 출력된다.
    - 다 더한 뒤, 총합으로 나누고, 100을 곱해서 %를 만든다.

    ```json
    {
      "Jupyter Notebook": 12491591,
      "HTML": 20989,
      "JavaScript": 10532,
      "SCSS": 9877,
      "Shell": 5458,
      "Python": 2772,
      "Smarty": 1965,
      "Ruby": 1945,
      "Makefile": 1422,
      "Dockerfile": 364
    }
    ```

1. `git.html`의 script부분(단순정보나열)을 퍼센트까지 뿌려주도록 변경된 스크립트를 복붙해주면 된다.

   - 모든 line수(values)를 sumcodeline에 더해주고, 각각의 value에다 나눈 뒤, 100을 곱한다.
   - strong태그로 출력을 해서, h2 아래에 추가해준다.
   - count변수를 사용해서 불러올 repo를 5개로 지정해준다. 최대갯수는 100개다.

   ```js
   // 사용한 언어를 return해주는 함수
   const repoLanguage = async (repo) => {
     let response = await fetch(
       `https://api.github.com/repos/is2js/${repo}/languages`,
       {
         headers: {
           Accept: 'application/vnd.github.v3+json',
         },
       }
     );
     let resJson = await response.json();
     let sumcodeline = Object.values(resJson).reduce((a, b) => a + b, 0);
     s = '';
     for (const [key, value] of Object.entries(resJson)) {
       s += `${key} : ${((value / sumcodeline) * 100).toFixed(3)}%  `;
     }
     return s;
   };

   // 비동기 작업을 위해 async function 선언
   const initRepo = async () => {
     // 추가할 dom선택
     const repoUl = document.getElementById('repos');
     //갯수설정
     const count = 5;
     // headers 설명 : 깃허브에 요청할 형식 지정
     let response = await fetch(
       'https://api.github.com/users/is2js/repos?' + 'per_page=' + count,
       {
         headers: {
           Accept: 'application/vnd.github.v3+json',
         },
       }
     );
     const repoJson = await response.json();
     // repo받아온 내용 확인
     // console.log(repoJson);

     // json내용을 map을 이용해 각 dom을 만들고 추가
     // repoLanguage 함수의 비동기 작업을 위한 async function선언
     repoJson.map(async (repos) => {
       let h2Element = document.createElement('h2');
       h2Element.textContent = repos.name;

       let pElementDesc = document.createElement('p');
       pElementDesc.textContent = '레포설명 : ' + repos.description;

       let aElementURL = document.createElement('a');
       aElementURL.href = repos.svn_url;
       aElementURL.textContent = repos.svn_url;

       //사용한 언어 불러오기
       let language = await repoLanguage(repos.name);
       let strongElementLang = document.createElement('strong');
       strongElementLang.textContent = '사용언어 : ' + language.toString();

       //각 요소를 <div id="repos">에 추가
       repoUl.append(h2Element);
       //사용언어부분 추가
       repoUl.append(strongElementLang);
       repoUl.append(pElementDesc);
       repoUl.append(aElementURL);
       repoUl.append(document.createElement('br'));
     });
   };
   initRepo();
   ```

### latex 사용 on

1. \_config.yml에서 LaTex 적용이 default가 true가 아니라서 아래와 같이 \_config.yml 파일에 use_math 옵션을 true로 변경해주시면 됩니다.

   ```yaml
   use_math: true
   ```

## 구조

###

- `index.html` : 첫 화면에 대한 내용을 md형식으로 닮고 있지만, html파일임.

  - `{{site.baseurl}}`/images/diagram.png "https://github.com/fastai/fastpages")

    ![image-20210816210351148](https://raw.githubusercontent.com/is3js/screenshots/main/image-20210816210351148.png)

    - **`{{site.baseurl}}`**은 나중에 is2js.github.io/`blog_raw`/ 폴더(**깃허브 블로그 원본 폴더**)로 연결된다.

- `_pages/` : md or html의 nav메뉴들의 모음

  - about.md, search.html 등
  - 추가된 md파일의 **양식속 `title:[ ]`**이 nav메뉴에 뜨는 페이지명이 된다.
    - resume.md
    - git.html

- `images/`

  - 사진, favicon
