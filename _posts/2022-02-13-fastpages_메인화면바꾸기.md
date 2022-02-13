\---

toc: true

layout: post

description:  home.html > index.html + posts의 메인화면 수정

categories: [fastpages]

title: change index.html



image: "images/posts/blog.png"



\---

- [참고블로그](https://anarinsk.github.io/lostineconomics-v2-1/coding-tool/web-tool/2020/03/07/blogging-with-fastpages.html) 및 [깃허브](https://github.com/anarinsk/lostineconomics-v2-1)
  - 이분은 옛날 버전인 것 같으며, index.md로 paginationd이 작동한다.
  - 그러나 내 버전의 _config.yml은 md파일을 html파일로 수정하라고 명시하고 있다.



### index.md를 posts pagination을 제공 안함.

- `index.md`를 사용하면, 아래 posts의 pagination이 안된다고 `_config.yml`에 명시되어있었다.
  - **그래서 index.md로 빌드는 되니, 빌드후 html소스를 복붙해서 `index.html`**로 옮기는 방식으로 꾸미려고 한다.
- index.html을 삭제한 상태에서 main으로 빌드 되어 표기는 되더라.



#### index.html -> index_backup.html로 빼놓기

- root에 index.html이 존재하면, index.md이 반영이 안되어 백업해놓는다.



#### index.md 생성 후 내용 채워 push

- 나름의 시행착오 끝에 완성한 `index.md`(생성해야함.)

```
---
layout: home
search_exclude: true

---

![]({{ site.baseurl }}/images/logo_github.png "로고"){: style="float: right; margin-left: 20px" width="180"}

### I'am...

평범한 한의사 `돌아온범생`의 우아한 엔지니어 도전기를 기록합니다.<br/>
<br/>
조재성 Jaeseong Cho | <br/>
Doctor of Korean Medicine, Currently studying SW engineering and biostatistics<br/>
[more...]({{ site.baseurl }}/about)


### Posts
```

- markdown에서 이미지를 float시키는 방법 참고해보자.

  `!()[]{: style="float: right; margin-left: 20px" width="180"}`

![image-20220213180207307](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220213180207307.png)

![image-20220213180236630](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220213180236630.png)





### 블로그 -> 소스보기 -> html코드로 복붙

```html
<main class="page-content" aria-label="Content">
      <div class="wrapper">
        <div class="home"><p><img src="/images/logo_github.png" alt="" title="로고" style="float: right; margin-left: 25px" width="200" /></p>

<h3 id="iam">I’am…</h3>

<p>평범한 한의사 <code class="language-plaintext highlighter-rouge">돌아온범생</code>의 우아한 엔지니어 도전기를 기록합니다.<br />
<br />
조재성 Jaeseong Cho | <br />
Doctor of Korean Medicine, Currently studying SW engineering and biostatistics<br />
<a href="/about">more…</a></p>

<h3 id="posts">Posts</h3>
```

![image-20220213180351406](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220213180351406.png)







### index.html 부활시키기 + md는 삭제

![image-20220213180423779](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220213180423779.png)



![image-20220213180443002](https://raw.githubusercontent.com/is3js/screenshots/main/image-20220213180443002.png)





```html
---
layout: home
search_exclude: true
image: images/logo_github.png
---

<main class="page-content" aria-label="Content">
  <div class="wrapper">
    <div class="home"><p><img src="/images/logo_github.png" alt="" title="로고" style="float: right; margin-left: 25px" width="200" /></p>

<h3 id="iam">I’am…</h3>

<p>평범한 한의사 <code class="language-plaintext highlighter-rouge">돌아온범생</code>의 우아한 엔지니어 도전기를 기록합니다.<br />
<br />
조재성 Jaeseong Cho | <br />
Doctor of Korean Medicine, Currently studying SW engineering and biostatistics<br />
<a href="/about">more…</a></p>

<h3 id="posts">Posts</h3>
```







