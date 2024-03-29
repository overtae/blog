---
title: "[파이썬 플라스크] 삽질 log..."
date: 2022-10-08
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "에러 해결 과정"
---

## 개요

인스타그램 클론 코딩을 위한 게시물 목록/상세 API 구현 중에 맞닥뜨린 에러에 대해 정리하고자 한다.

들어가기 전에, 해당 포스팅은 백엔드에 대해 지네 발톱의 때 만큼도 알지 못하는 사람이 기술했다는 점을 머리와 마음 두 곳에 모두 새겨두길 바란다..

전체 코드는 [이 곳](https://github.com/overtae/my-flask-study/tree/11-flask-instagram)에서 확인 가능하다.

## Undefined field

모든 것이 완벽했었다. (완벽한 줄 알았다.)

<figure style="width: 200px;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/instagram/err01-01.png" alt="">
  <figcaption>이 오류를 마주하기 전까지는..</figcaption>
</figure>

게시물 생성 api 구현 중에 해당 오류가 발생하였다.

따라서 api 디렉토리의 post 관련 파일들을 하나씩 점검해보기 시작했다.

> 컬럼이 누락되었나?

<figure style="width: 500px;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/instagram/err01-02.png" alt="">
  <figcaption>post 모델에 <code>author_id</code> 컬럼이 존재하기 때문에 누락 문제는 아닐 것이다.</figcaption>
</figure>


> import를 잘못했나?

<figure style="width: 300px;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/instagram/err01-03.png" alt="">
  <figcaption>폴더 구조와 대조해보았을 때 일치하므로 import 문제는 아닐 것이다.</figcaption>
</figure>


> db에 저장은 성공하지 않았을까?

텅 빈 db만이 나를 반겨주었다.

> 오타가 있을까?

있었다.

<figure style="width: 200px;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/instagram/err01-04.png" alt="">
</figure>

`schemas/PostSchema/post.py` 파일에 `include_fk`를 `inculde_fk`로 입력하여 안되던 것이었다..

<figure style="width: 250px;" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/instagram/err01-05.png" alt="">
  <figcaption>이후에는 정상적으로 게시물이 생성된다.</figcaption>
</figure>


## KeyError: 'JWT_SECRET_KEY'

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/instagram/err02-01.png" alt="">
</figure>

테스트 코드 실행 시 vsc의 테스트 탭을 이용했는데, 분명히 JWT_SECRET_KEY가 존재하는데도 없다고 오류가 발생하였다.

오류 검색을 해봐도 `JWT_SECRET_KEY = '...'`를 추가하라는 글이 많았다.

`os.environ["JWT_SECRET_KEY"]` 대신 그냥 문자열을 넣어보기도 했는데도 같은 오류가 발생했다.

여러가지 찾아보다 이전에 블로그 개발할 때 vsc의 테스트 기능에 데인 경험을 떠올리며 터미널로 실행해보았다.

위에 뭐라 말이 많긴 하지만 테스트에 성공했다.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/instagram/err02-04.png" alt="">
  <figcaption>야호.</figcaption>
</figure>