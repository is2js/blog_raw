---
toc: true
layout: post
description: 폰트와 tags 페이지 수정해보자.
categories: [fastpages]
title: fastpages font & tag page change

image: "images/posts/blog.png"

---

### font 바꾸기

- 참고: [한국 블로그](https://anarinsk.github.io/lostineconomics-v2-1/coding-tool/web-tool/2020/03/07/blogging-with-fastpages.html)


1. `_includes`폴더 > `head.html`파일에서 **웹 폰트 link태그 추가해주기**

    ```html
    <!-- 폰트추가를 위한 link태그 삽입 -->
    <!-- 폰트1:  기본 spoqa 웹폰트 -->
    <!-- css에서 * {} 다 뒤집어씀. -->
    <link href='//spoqa.github.io/spoqa-han-sans/css/SpoqaHanSansNeo.css' rel='stylesheet' type='text/css'>
    <!-- 폰트2:  블로그 제목들 sunflower 웹폰트 -->
    <!-- 쥬피터 등 포스트 내부 글자 h1, h2 제목은 sunflower체 도입 -->
    <link href="//fonts.googleapis.com/css?family=Sunflower:300,500,700" rel="stylesheet"> 
    ```

2. 해당 폰트를 사용하기 위해 `assets`폴더 > `css`폴더 > **`styles.css`를 수정**한다
    - 나의 경우, scss수정시 잘 안됬다. 모든 것을 여기서 설정해줬다.
    - font-family 검색해서 모두 `font-family: 'Spoqa Han Sans Neo', normal;`로 바꿔주기
    - 전역으로 다 먹이기 + 제목들은 다른 글자체로 먹이기
        ```css
      /* 제목들은 먹히는데, 제목만쓰질 않으니.. */
      /* test-> 전체를 font-familty 덮어쓰기 */
      *{
        font-family:'Spoqa Han Sans Neo', normal!important; 
      }
      /* font2 제목 글자는 선플라워  */
      h1, h2, h3, h4, h5, h6, tag-name {
        font-family:"Sunflower", normal!important; 
        color: #152447;
      }
        ```


3. **a태그 리셋시켜서 색 적용되게 하기**
    ```css
    /** Links */
    /** 폰트 색 (링크 색) -> 이것만 바꾼다고 변경안됨.. */
    /* 모든 a태그 프로퍼티들을 리셋 */
    a {
      all: unset;
    }
    /* a태그의 가상클래스도 같이 리셋 */
    a:link {
      text-decoration: none;
      color: #152447;
      }
      a:visited {
      text-decoration: none;
      color: #152447;
      }
      /* 누른상태 */
      a:active {
      text-decoration: none;
      color: #152447;
      }
      /* 갖다덴 상태 */
      a:hover {
      text-decoration: none;
      color: #152447;
      }

    a { color: #152447; text-decoration: none; }
    a:visited { color: #152447; }
    a:hover { color: #313266; text-decoration: underline; }

    ```

4. 마크다운 강조색을 빨간색 글자로 변경
    ```css
    pre, code { font-family: 'Spoqa Han Sans Neo', "Menlo", "Inconsolata", "Consolas", "Roboto Mono", "Ubuntu Mono", "Liberation Mono", "Courier New", monospace; font-size: 0.9375em; border: 1px solid #f9f2f4; border-radius: 3px; background-color: #f9f2f4; color:#E53A40;}
    ```

### tags.html 수정하기

1. 예전 블로그의 css를 가져와 적용할 준비를 한다.
    `style.css`
    ```css
    /** 예전 블로그에서 쓰던 tag모음 + 각 개별tag적용 css  */
    .entry-meta {
        font-size: 12px;
        font-size: 0.75rem;
        text-transform: uppercase;
        color: #152447; }
    .entry-meta a {
        color: #152447; }
    .entry-meta .vcard:before {
        content: " by "; }
      
    .entry-meta .tag {
        display: inline-block;
        margin: 4px;
        color: #fff;
        border-radius: 3px;
        background-color: rgba(162, 162, 162, 0.8); }

    /*my) 상단 태그들 모음 tag1 shap(#) */
    .entry-meta .tag span {
        float: left;
        padding: 6px 6px;
        color: #152447;
        font-family: inherit;
        /* background-color: #ffffff; */
    }
    /*my) 상단 태그들의 tag2 name(태그명) 색*/
    .entry-meta .tag .tag-name {
      color: #ffffff;
        background-color: #152447;
        border-radius: 0 3px 3px 0;
        /*3px 이상 오그리면, 하이퍼링크랑 막 난리남.*/
    }
    .entry-meta .tag:hover {
        background-color: rgba(136, 136, 136, 0.8); }
    .entry-meta .entry-reading-time {
        float: right; }
        

    .inline-list {
        list-style: none;
        margin-left: 0;
        padding-left: 0; 
    }
    .inline-list li {
        list-style-type: none;
        display: inline; 
    }

    /* 추가) 각 제목들도 tag 작은모양처럼*/
    .eachtag {
      color: #ffffff;
        background-color: #152447;
        border-radius: 0 3px 3px 0;
    }

    ```

2. tags.html코드를 수정한다.
    - ul태그에 `entry-meta inline-list` 속성을 추가한다
    - 그외 tag, tag-name, eachtag 등의 속성을 이용해서 꾸민다.
    
    ```html
    <ul class="entry-meta inline-list">
    {% for category in categories %}
      <li ><a class="tag" href="#{{ category }}"> 
        <span># </span>
        <span class="tag-name">{{ category }}</span></a>
      </li>
    {% endfor %}
    </ul>

    {% for category in categories %}
        <h4 id ="{{ category }}">
          <span class="eachtag tag-name">&nbsp;#&nbsp;{{ category }}&nbsp;&nbsp;</span></li>
        </h4>
        <a name="{{ category | slugize }}"></a>
        {% for post in site.categories[category] %}
          {% if post.hide != true %}
          {%- assign date_format = site.minima.date_format | default: "%b %-d, %Y" -%}
          <article class="archive-item">
            <p class="post-meta post-meta-title"><a class="page-meta" href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a>  • {{ post.date | date: date_format }}</p>
          </article>
          {% endif %}
        {% endfor %}
    {% endfor %}
    ```
