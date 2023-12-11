---
toc: true
layout: post
title: fastpages utterances 적용
description: fastpages 블로그 문제점 2가지 디버깅

categories: [fastpages, utterances]
image: "images/posts/blog.png"
---

### 공통사항

1. github 로그인 상태에서 https://github.com/apps/utterances  에 접속하여, configure를 선택

   - 혹은 settings > Integrations > Applications 에 들어가면 된다.

2. 기본 설정된 `Only select repositories`를 그대로 선택한 뒤, `blog repository`를 선택하여 `save`한다.

   - **gitblog repository가 아닌 경우(private repo or tistory 등은 새로운 레포지토리를 파서 등록해야한다)**
   - 이 때 all repositories를 고르면, 댓글들이 모든 레포의 issue에 등록되는 사고가 발생할 수 있다.

   ![image-20231211231330620](https://raw.githubusercontent.com/is2js/screenshots/main/image-20231211231330620.png)



3. 나는 2년전에 설치했다고 해서, install버튼이 없어서, 직접 공식홈페이지 [https://utteranc.es](https://utteranc.es/) 로 이동했다.

   - 아니라면 `install`버튼을 클릭후, 자동으로 이동된다.

   - 이후, `repo:`에는 owner/repo명을 적으면 되고 / `Mapping`부분에는 첫번째 것 선택을 그대로 두면 된다.

     ![image-20231211232319052](https://raw.githubusercontent.com/is2js/screenshots/main/image-20231211232319052.png)



#### 방법1) js복붙

1. 테마를 선택하고 / js코드를 복사한다.

   ![image-20231211232425112](https://raw.githubusercontent.com/is2js/screenshots/main/image-20231211232425112.png)

2. `_layout/post.html`의 맨 아래에 복붙한다.

   ![image-20231211234020808](https://raw.githubusercontent.com/is2js/screenshots/main/image-20231211234020808.png)





#### 2) fastpages을 사용하는 경우

1. `post.html`의 구조 살펴보기

   ```html
     <div class="post-content e-content" itemprop="articleBody">
       <!-- toc가 먼저 나오므로 h3로 안내하기 -->
       <h3>📜 제목으로 보기</h3>
       {{ content | toc  }}
     </div>
     {%- if page.comments -%}
       {%- include utterances.html -%}
     {%- endif -%}
     {%- if site.disqus.shortname -%}
       {%- include disqus_comments.html -%}
     {%- endif -%}
     <a class="u-url" href="{{ page.url | relative_url }}" hidden></a>
   </article>
   ```

   - **각 post.md에 `comments: true` 옵션을 넣어주면, `_includes/utterances.html` 페이지가 include된다.**



2. `includes/utterances.html` 페이지에 아래와 같이 `_config.yml`의 변수들을 사용하여, 자동으로 `js복붙`과 같은 코드가 구성되는 것 같다.

   ```html
   <!-- from https://github.com/utterance/utterances  -->
   <script src="https://utteranc.es/client.js"
           repo="{{ site.github_username }}/{{ site.github_repo | default: site.baseurl | remove: "/" }}"
           issue-term="comments"
           label="blogpost-comment"
           theme="github-light"
           crossorigin="anonymous"
           async>
   </script>
   ```

   ![image-20231212003953132](https://raw.githubusercontent.com/is2js/screenshots/main/image-20231212003953132.png)

3. **조심) 나는 `_config.yml`에서 github.io의 `url`을 custom domain으로 바꿔줬는데, `https://`의 프로토콜을 안붙혀줬더니, github 로그인후 redirect시 잘못된 주소가 계속 찍혔다**

   ```yml
   # url: "https://is2js.github.io" # the base hostname & protocol for your site, e.g. http://example.com
   url: "https://blog.chojaeseong.com" # the base hostname & protocol for your site, e.g. http://example.com
   ```







### fastpaes comments:true인 경우, 댓글바로가기 삽입

- `_layout/post.html`에 추가

  ```html
    <div class="post-content e-content" itemprop="articleBody">
      <!-- toc가 먼저 나오므로 h3로 안내하기 -->
      <h3>📜 제목으로 보기</h3>
      {%- if page.comments -%}
      <a href="#댓글">🖊 댓글 바로가기</a>
      {%- endif -%}
      {{ content | toc  }}
    </div>
  ```

  