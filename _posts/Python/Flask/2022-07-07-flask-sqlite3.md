---
title: "[파이썬 플라스크] 파이썬에서 데이터베이스 사용하기"
date: 2022-07-07
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "sqlite3 모듈을 이용하여 데이터베이스를 사용해보자"
---

## Python DB API (PEP 249)

PEP란 Python Enhancement Proposal의 준말로 간단히 정보 전달을 위한 문서로 생각하면 될 것 같다.

그럼 PEP의 249번 문서는 어떤 정보를 전달하고 있을까.

[PEP 249](https://peps.python.org/pep-0249/) 소개 문구를 확인해보자.

![PEP 249]({{ site.url }}{{ site.baseurl }}/assets/images/python/flask/sqlite/pep249.PNG){: .align-center}

영어지만 정말 간단하게 해석을 해보자면 모듈 간의 유사성(similarity)을 장려하기 위해 정의되었다고 한다.

**이해하기 쉬운 코드**, **이식성이 높은 코드**를 작성하려면 같은 역할을 하는 메소드끼리는 메소드명이 같으면 편할 것이다.

즉, A 역할에는 A라는 메소드명을 사용하라고 적혀있는 문서이다.

## sqlite3 모듈

이를 알기 전에 먼저 SQLite가 무엇인지 알아보도록 하자.

[위키백과](https://ko.wikipedia.org/wiki/SQLite)에 따르면 SQLite는 데이터베이스 관리 시스템으로, 서버가 아닌 응용프로그램에 넣어서 사용하는 비교적 가벼운 데이터베이스라고 한다.

이 SQLite에 대한 인터페이스를 제공해주는 것이 표준라이브러리인 sqlite3 모듈인 것이다.

sqlite3 모듈을 사용할 경우 따로 모듈을 설치할 필요 없이 데이터베이스를 쉽게 이용할 수 있다.

## 간단한 질의 날려보기

### 데이터베이스 생성

---

먼저, 터미널에 `python` 입력 후 파이썬 콘솔로 이동한다.

이후 아래와 같은 코드를 입력해준다.

{% highlight python linenos %}

import sqlite3 # sqlite3 import
con = sqlite3.connect("test_database.db") # db 생성

{% endhighlight %}

#### 결과

<figure>
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/sqlite/conDB_rst.PNG" alt="">
  <figcaption>파일이 생성되었다.</figcaption>
</figure>

### 테이블 생성

---

테스트로 영화의 정보를 저장하는 테이블을 만들어보자.

아래와 같이 입력해주면 된다.

{% highlight python linenos %}

cur = con.cursor() # cursor 연결

cur.execute('''CREATE TABLE Movie
                (title text, genre text, open_date date, show_time integer)''')

{% endhighlight %}

### 자료형

아래와 같은 자료형이 있다고 한다.

```
NULL: null 값

INTEGER: 1, 2, 3, 4, 6, 8 bytes 의 정수 값 (0과 1로 구분하여 Boolean 값을 다룬다.)

REAL: 8 bytes 의 부동소수점 값

TEXT: 문자열

BLOB: 입력된 그대로 저장 (바이너리 파일 등 >> 일반적으로 멀티미디어 파일을 저장하는 용도)
```

#### 결과

<figure>
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/sqlite/crtTable_rst.PNG" alt="">
  <figcaption>잘 생성된 것을 확인할 수 있다.</figcaption>
</figure>

### 데이터 추가

---

임의의 영화 데이터를 추가해주었다.

이때, 따로 `test_query.py` 파일을 만들어 실행해주었다.

{% highlight python linenos %}

import sqlite3

con = sqlite3.connect('test_database.db')
cur = con.cursor()

cur.execute("INSERT INTO Movie VALUES ('범죄도시2', '범죄', '2022-05-18', 106)")

con.commit()
con.close()

{% endhighlight %}

파일 작성 후 `python 파일이름`을 해준다면 테이블 내에 데이터가 들어갈 것이다.

#### 결과

<figure>
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/sqlite/addTable_rst.PNG" alt="">
  <figcaption>신기하다.</figcaption>
</figure>

