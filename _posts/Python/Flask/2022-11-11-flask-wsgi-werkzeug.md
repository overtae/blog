---
title: "[파이썬 플라스크] WSGI 란?"
date: 2022-11-11
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "Flask Instagram Clone Coding"
---

## Web Server Gateway Interface, WSGI

WSGI란 무엇일까.

[공식 문서](https://wsgi.readthedocs.io/en/latest/what.html)에는 “*웹 서버가 웹 애플리케이션과 통신하는 방법과 웹 애플리케이션을 연결하여 하나의 요청을 처리하는 방법에 대한 설명서”* 라고 나와있다.

여러 종류의 웹 서버와 여러 종류의 웹 애플리케이션이 모두 통신이 가능하도록 하기 위한 규약이라고 보면 된다. 이를 이해하기 위해 먼저 웹 서버와 웹 애플리케이션에 대해 알아보자.

### 웹 서버와 웹 애플리케이션

---

**💡 참고:** 웹 서버는 하드웨어와 소프트웨어 측면의 의미가 있는데, 아래에서는 소프트웨어로서의 의미를 다룬다. — [관련 링크](https://developer.mozilla.org/ko/docs/Learn/Common_questions/What_is_a_web_server)
{: .notice--success}

웹 서버는 **클라이언트의 정적 웹 페이지 요청을 처리**하는 프로그램을 말한다. 대표적인 웹 서버로는 Apache, Nginx 등이 있다.

웹 애플리케이션(또는 웹 애플리케이션 서버, WAS)은 **웹 서버가 동적 웹 페이지 요청을 처리할 수 있게 도와주는 역할**을 한다. (웹 애플리케이션이 해당 요청을 처리한 결과를 웹 서버에게 돌려준다.)

<details>
<summary>정적 웹 페이지와 동적 웹 페이지</summary>
<div markdown="1">
    
정적 웹 페이지는 서버에 **이미 만들어진 (저장된) 페이지나 파일이 그대로 전달되는 페이지**이고, 동적 웹 페이지는 서버의 데이터를 **스크립트가 가공하여 만들어 주는 페이지**이다. 언제 어디서든 같은 페이지를 보여준다면 정적 웹 페이지, 사용자의 요청이나 시간 등에 따라 변한다면 동적 웹 페이지이다.

~~*사실 간단하게 구분하자면 데이터베이스의 유무가 되지 않을까..*~~

- 정적 웹 페이지 : 회사 소개, 학교 소개, …
- 동적 웹 페이지 : 게시판, 뉴스, 날씨, …

</div>
</details>

여기서 핵심은 동적인 웹 페이지를 만들기 위해서는 **웹 서버와 웹 애플리케이션이 서로 소통**을 해야 한다는 것이다.

지금까지 Flask를 이용하여 웹 애플리케이션을 만들어보았다. 이제 웹 서버와 소통을 하면 되는데, **대부분의 웹 서버는 파이썬 코드를 이해하지 못한다**고 한다. 그렇기 때문에 **WSGI**가 필요한 것이다.

### WSGI 역할

---

위에서 웹 서버는 파이썬 프로그램을 호출하지 못한다고 했다. 그러나 WSGI를 이용한다면 가능하다. 흐름은 아래와 같다.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/instagram/wsgi.png" alt="">
</figure>

WSGI 서버: **파이썬 프로그램 호출이 가능**한 서버, 대표적으로 uWSGI, Gunicorn 등이 있다.

WSGI 애플리케이션: WSGI를 만족하는 웹 애플리케이션, Flask, django 등이 있다.

<details>
<summary>WSGI를 만족?</summary>
<div markdown="1">

- 웹 서버는 요청 정보를 Callable Object를 통해 애플리케이션에 전달한다.
    - Callable Object는 함수나 객체가 될 수 있다.
    - HTTP Request 정보와 Callback 함수를 전달해야 한다.
- 웹 애플리케이션은 요청을 처리 후, 웹 서버에 반환한다.
    - HTTP Request → 요청 처리
    - Callback Funtion → 결과 반환

위와 같은 인터페이스를 구현한 애플리케이션을 WSGI 애플리케이션이라고 한다.

직접 구현할 필요는 없다. [WSGI Middleware](#wsgi-middleware)가 제공하기 때문이다.

</div>
</details>
    
1. 클라이언트가 동적 웹 페이지 요청을 보낸다.
2. 웹 서버는 WSGI 서버를 호출한다.
3. WSGI 서버는 WSGI 애플리케이션을 호출한다.
4. WSGI 애플리케이션은 요청을 처리한다.
5. 수행 결과를 WSGI 서버 → 웹 서버 → 클라이언트에게 보낸다.

## WSGI Middleware

WSGI 자체로는 서버나 프레임워크가 아니다. 파이썬 프로그램을 이해할 수 있게 해주는 **번역기 역할**을 하는 것이다. 그렇기 때문에 웹 개발 시 공통적으로 필요한 기능들(라우팅, 인증, 세션, 쿠키 등)을 매번 구현해야 한다.

WSGI 미들웨어는 웹 애플리케이션 실행 전, 후에 위에서 말한 기능들을 추가해준다. 또한 그 자체로도 WSGI 애플리케이션이다.

WSGI 미들웨어의 역할을 하는 라이브러리로는 *uWSGI, Gunicorn, Werkzeug* 등이 있다.

### Werkzeug

---

Werkzeug는 Flask에서 기본적으로 사용하는 WSGI 미들웨어로, WSGI 미들웨어 역할 뿐만 아니라 **웹 서버의 역할**을 할 수도 있다. 따라서 별다른 설정 없이도 Flask 앱을 실행할 수 있다.

따라서 Flask는 이미 WSGI 애플리케이션이라고 할 수 있다. 하지만 단순한 개발용 서버를 내장하고 있기 때문에 프로덕션에서는 다른 것으로 대체해야 한다고 한다. 

## 요약

웹 서버는 정적인 페이지만 처리할 수 있다. 동적인 페이지는 웹 애플리케이션을 통해 처리하게 된다.

WSGI는 웹 서버와 웹 애플리케이션이 통신하기 위한 규약이라 생각하면 된다. 대부분의 웹 서버는 파이썬 프로그램을 이해하지 못하기 때문이다. WSGI라는 번역기가 웹 서버와 파이썬 프로그램이 소통할 수 있게 해준다.

### 참고

---

[https://tibetsandfox.tistory.com/22](https://tibetsandfox.tistory.com/22)

[https://wikidocs.net/75556#wsgi](https://wikidocs.net/75556#wsgi)

[https://velog.io/@han0707/WSGI는-무엇일까](https://velog.io/@han0707/WSGI%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C)

[https://spoqa.github.io/2012/01/16/wsgi-and-flask.html](https://spoqa.github.io/2012/01/16/wsgi-and-flask.html)

[https://sgc109.github.io/2020/08/15/python-wsgi/](https://sgc109.github.io/2020/08/15/python-wsgi/)

[https://june-coder.tistory.com/53](https://june-coder.tistory.com/53)