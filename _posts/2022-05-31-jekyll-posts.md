---
title: "jekyll: Posts"
categories:
- jekyll
---

# Posts

- [jekyll - Posts](https://jekyllrb.com/docs/posts/)

## 포스트 폴더

- _posts 폴더
- 마크다운이나 HTML로 작성

## 포스트 생성

YEAR-MONTH-DAY-title.MARKUP 포맷으로 _posts 폴더 안에 생성

```
2011-12-31-new-years-eve-is-awesome.md
2012-09-12-how-to-write-a-blog.md
```
예를 들면 위와 같이 생성

```
---
layout: post
title:  "Welcome to Jekyll!"
---

# Welcome

**Hello world**, this is my first Jekyll blog post.

I hope you like it!
```

모든 포스트는 위와 같이 front matter로 시작되야 함

## 이미지, 리소스 추가

assets 폴더 안에 이미지, 파일 등을 추가하고 불러오면 됨

### 이미지 추가
```
... which is shown in the screenshot below:
![My helpful screenshot](/assets/screenshot.jpg)
```

### PDF 추가
```
... you can [get the PDF](/assets/mydoc.pdf) directly.
```

## 블로그 포스트 링크 목록 만드는 방법

```liquid
<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
```

post 변수는 for 문 안에, 만약 페이지를 하고 싶다면 post 자리에 page를 넣으면 됨

## 태그와 카테고리

### 태그

태그는 front matter에 tag 혹은 다수면 tags 키로 써주면 된다.

```
tag: classic hollywood
```
의 경우 tag 단수이기 때문에 "classic hollywood"로 태그가 지정된다.

```
tags: classic hollywood
```
의 경우 tags 복수이기 때문에 ["classic", "hollywood"]로 태그가 지정된다.

```liquid
{% for tag in site.tags %}
  <h3>{{ tag[0] }}</h3>
  <ul>
    {% for post in tag[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}
```

### 카테고리

- 카테고리도 front matter에 category 혹은 복수면 categories 키로 써주면 된다. (태그랑 똑같음)
- 모든 카테고리는 Liquid templates에 site.categories 로 노출된다. (태그랑 똑같음)

```
movies/horror/_posts/2019-05-21-bride-of-chucky.markdown 
```
의 경우 자동으로 movies, horror가 이 포스트의 카테고리로 등록된다.

_posts 위의 모든 디렉토리는 카테고리로 읽혀진다.

```
category: classic hollywood

movies/horror/classic%20hollywood/2019/05/21/bride-of-chucky.html
```
같은 표현

```
categories: classic hollywood

movies/horror/classic/hollywood/2019/05/21/bride-of-chucky.html
```

같은 표현

> Post excerpts 부터 하면 됨

