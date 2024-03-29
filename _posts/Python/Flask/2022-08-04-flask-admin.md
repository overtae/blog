---
title: "[파이썬 플라스크] 블로그 웹 애플리케이션 개발 - 관리자 페이지, 카테고리, 게시물"
date: 2022-08-03
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "관리자 페이지, 카테고리, 게시물 기능 처리"
---

## 테스트 주도 개발 TDD

이제까지는 앱을 직접 실행해 코드를 확인했었다.

하지만 지금부터는 테스트 코드를 먼저 작성하고 실제 코드를 작성해볼 것이다.

이를 **테스트 주도 개발(Test Driven Development, TDD)**이라고 한다.

**테스트 주도 개발**은 소프트웨어를 개발하는 여러 방법론 중 하나로 아래와 같은 과정을 거친다.

1. 해당 기능이 정상적으로 움직이는지 검증하기 위한 테스트 코드 작성
2. 테스트 실패시 통과할 수 있게 최소한으로 코드 개선
3. 테스트에 성공한 코드를 표준에 맞게 리팩토링

![tdd]({{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/tdd.png){: .align-center}


### 왜 쓰는가?

---

테스트 주도 개발은 **기능 단위로 테스트**를 하기 때문에 **빠른 피드백**이 가능하다.

이처럼 문제가 없는지 사용자에게 서비스하기 전에 먼저 진단 받을 수 있기 때문에 **코드가 지닌 불안정성과 불확실성을 지속적으로 해소**할 수 있다.

또한 테스트를 통과하기 위한 **최소한의 코드만 작성 및 개선**하기 때문에 **오버 코딩을 방지**할 수 있다.

마지막으로, 개발 과정이 테스트 코드로 남기 때문에 과거에 했던 의사결정을 쉽게 확인할 수 있다.

참고 링크 : [패스트캠퍼스](https://media.fastcampus.co.kr/knowledge/dev/tdd/)

## 스태프 권한 추가

유저 중 스태프 권한이 있는 경우에만 어드민 페이지에 접근이 가능하고, 회원 관리를 할 수 있게끔 할 것이다.

그러기 위해서는 먼저 User 모델에 스태프인지를 판별하는 불리언 필드를 추가해주어야 한다.

<details open>
<summary>models.py</summary>
<div markdown="1">

{% highlight python linenos %}
class User(db.Model, UserMixin):
    ...
    # 스태프 권한이 있는 유저인지 아닌지를 판별, Boolean
    is_staff = db.Column(db.Boolean, default=False)
{% endhighlight %}

</div>
</details>


## 회원가입, 로그인, 로그아웃 TDD

TDD 진행 방식을 좀 더 자세히 알아보기 위해 저번에 구현했었던 회원가입, 로그인, 로그아웃 기능을 테스트해보자.

하지만 그 전에, `unittest` 라는 프레임워크에 대해 간단하게 알아보자.

### unittest

---

파이썬 [단위 테스트 프레임워크](https://docs.python.org/ko/3/library/unittest.html#classes-and-functions) 문서를 살펴보면 어떤 메소드를 갖고 있는지 알 수 있다.

<details open>
<summary>tests.py</summary>
<div markdown="1">

{% highlight python linenos %}
import unittest
# unittest의 TestCase 클래스 상속
class TestCode(unittest.TestCase):
	def setUp():
		# 테스트 수행 전 자동으로 호출
	def tearDown():
		# 테스트 끝난 후 자동으로 호출
	def MyTest():
		# 테스트 하고 싶은 코드
{% endhighlight %}

</div>
</details>


### 돌아와서…

---

테스트 과정은 아래와 같다.

```
# 회원가입, 로그인, 로그아웃 테스트
1. N명의 유저를 db에 넣고 db에 N명의 유저가 존재하는지 확인
2. signup 폼으로 회원가입 요청을 보내면 db에 값이 맞게 들어가는지 확인
3. 로그인 전/후로 네비게이션 바가 동적으로 바뀌는지 확인
	- 비로그인시 네비게이션 바에 login, sign up 메뉴 표시 / 로그인시 유저 이름과 logout 표시
```

위 3가지를 테스트하기 전 데이터베이스를 설정해주겠다.

<details open>
<summary>tests.py</summary>
<div markdown="1">

{% highlight python linenos %}
import unittest
from os import path

from flask_login import current_user

from blog import create_app
from blog import db
import os

from blog.models import User

basedir = os.path.abspath(os.path.dirname(__file__))
app = create_app()
app.testing = True

class TestAuth(unittest.TestCase):
	def setUp(self):
		self.ctx = app.app_context()
		self.ctx.push()
		self.client = app.test_client()
		# 테스트를 위한 db 설정
		self.db_uri = 'sqlite:///' + os.path.join(basedir, 'test.db')
		app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = False
		app.config['TESTING'] = True
		app.config['WTF_CSRF_ENABLED'] = False
		app.config['SQLALCHEMY_DATABASE_URI'] = self.db_uri
		if not path.exists("tests/" + "test_db"):
			db.create_all(app=app)
		db.session.close()

	# 다른 테스트들에 영향을 줄 수 있기 때문에 테스트 후 데이터베이스 삭제
	def tearDown(self):
		os.remove('test.db')
		self.ctx.pop()
{% endhighlight %}

</div>
</details>

`os.path.abspath` 의 경우 파일의 경로를 명시해준다. — [링크](https://docs.python.org/3/library/os.path.html) 참고

### 1. N명의 유저를 db에 넣고 db에 N명의 유저가 존재하는지 확인

---

{% highlight python linenos %}
def test_signup_by_datebase(self):
  self.user_test_1 = User(
    email='testemail1@test.com',
    username='testUser01',
    password='qwerasdf',
    is_staff=True
  )
  db.session.add(self.user_test_1)
  db.session.commit()

  self.user_test_2 = User(
    email='testemail2@test.com',
    username='testUser02',
    password='qwerasdf'
  )
  db.session.add(self.user_test_2)
  db.session.commit()

  self.assertEqual(User.query.count(), 2)

  db.session.close()
{% endhighlight %}

테스트 할 **메소드명은 반드시 `test`로 시작**해야 누락되지 않고 테스트 케이스로 인식이 된다고 한다.

마지막 행의 `assertEqual()`는 첫번째 인자와 두번째 인자가 같은지를 검사하는 메소드로 자세히 알고 싶다면 [문서](https://docs.python.org/ko/3/library/unittest.html#unittest.TestCase.assertEqual)를 참고하면 된다.

결과적으로 마지막 행은 **db에 있는 유저의 수가 2인지 검사**하는 코드이다.

테스트를 실행할 때 터미널에서 `python -m unittest tests/tests.py` 로 실행하는 방법과 VSCode의 테스트 탭을 이용하는 방법이 있다.

<details>
<summary>터미널로 테스트 실행</summary>
<div markdown="1">

{% highlight python linenos %}
터미널에 `python -m unittest tests/tests.py` 을 적어준다.

![terminal-test]({{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/terminal-test.png){: .align-center}

OK가 나왔다면 성공한 것이다.
{% endhighlight %}

</div>
</details>


<details>
<summary>VSCode로 테스트 실행</summary>
<div markdown="1">

VSCode에는 왼쪽에 테스트 탭이 있다.

![vscode-test]({{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/vscode-test.png){: .align-center}

테스트 파일 경로를 설정해준다면 쉽게 테스트 코드를 실행해볼 수 있다.

```
{
    "python.testing.unittestArgs": ["-v", "-s", "tests", "-p", "*.py"],
    "python.testing.pytestEnabled": false,
    "python.testing.unittestEnabled": true
}
// -s : 폴더 경로
// -p : 테스트 파일 이름 명시
```

</div>
</details>

### 2. signup 폼으로 회원가입 요청을 보내면 db에 값이 맞게 들어가는지 확인

---

{% highlight python linenos %}
def test_signup_by_form(self):
	response = self.client.post('/auth/sign-up', data=dict(
		email="testemail@test.com", username="testUser00", password1="qwerasdf", password2="qwerasdf"))
	self.assertEqual(User.query.count(), 1)
	db.session.close()
{% endhighlight %}

여기서 **response**는 브라우저에서 폼을 작성, 제출하는 것과 같다.

하나하나 완벽하게 알지는 못해도 클라이언트에 폼에 입력한 data들로 POST 요청을 보낸다는 것은 알 수 있을 것이다.

요청을 보낸다면 저번에 작성했던 `auth.py`에 `signup()`이 동작하면서 이메일, 유저 네임 중복 검사, home으로 리다이렉트가 될 것이다.

성공했다면 데이터베이스에 유저는 1명이 되어야 한다.

*이전 테스트 코드(`test_signup_by_datebase`)에서 2명을 추가했는데?*

테스트 케이스 하나가 끝날 때마다 데이터베이스는 초기화되기 때문에 결과적으로 한 명이 있는게 맞다.

<figure style="width: 200px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/test-01.png" alt="">
  <figcaption>성공했다.</figcaption>
</figure>


### 3. 로그인 전/후로 네비게이션 바가 동적으로 바뀌는지 확인

---

네비게이션 바는 html 요소인데, 그 안의 텍스트는 어떻게 확인해야할까?

이때 사용하는 것이 **BeautifulSoup**이다.

텍스트로 된 html에서 특정 태그를 추출할 때 사용하며 설치는 아래와 같다.

```bash
$ pip install beautifulsoup4
```

설치를 해주고 import까지 해주면 완벽하다.

{% highlight python linenos %}
from bs4 import BeautifulSoup

def test_before_login(self):
	# 로그인 전의 네비게이션 바 크롤링
	response = self.client.get('/')
	soup = BeautifulSoup(response.data, 'html.parser')
	navbar_before_login = soup.nav

	# 로그인과 회원가입 버튼이 있는지, 로그아웃 버튼이 없는지 확인
	self.assertIn("Login", navbar_before_login.text)
	self.assertIn("Sign Up", navbar_before_login.text, )
	self.assertNotIn("Logout", navbar_before_login.text, )

	# 회원가입 후,
	response = self.client.post('/auth/sign-up', data=dict(
		email="testemail@test.com", username="testUser00", password1="qwerasdf", password2="qwerasdf"))

	with self.client:
			# 로그인
			response = self.client.post('/auth/login', data=dict(
				email="testemail@test.com", username="testUser00", password="qwerasdf"),
				follow_redirects=True)
			# 로그인 후의 네비게이션 바 크롤링
			soup = BeautifulSoup(response.data, 'html.parser')
			navbar_after_login = soup.nav

			# 로그인 후 네비게이션 바에 유저 이름과 로그아웃 버튼이 있는지,
			# 회원가입 버튼과 로그인 버튼이 있는지 확인
			self.assertIn(current_user.username, navbar_after_login.text)
			self.assertIn("Logout", navbar_after_login.text)
			self.assertNotIn("Login", navbar_after_login.text)
			self.assertNotIn("Sign up", navbar_after_login.text)
{% endhighlight %}

위 코드를 추가해주고 테스트를 실행한다면…

<figure style="width: 200px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/test-02.png" alt="">
  <figcaption>문제 없이 성공했다.</figcaption>
</figure>

문제 없이 성공했다.


## 관리자 페이지: flask-admin

flask-admin을 사용해 관리자 페이지를 만들어볼 것이다.

그 전에 라이브러리 설치와 약간의 코드를 추가해주자.

```bash
$ pip install flask-admin
$ pip install flask-appbuilder
```

<details open>
<summary>__init__.py</summary>
<div markdown="1">

{% highlight python linenos %}
from flask_admin import Admin
from flask_appbuilder import ModelView
...
def create_app():
	...
	app.config['FLASK_ADMIN_SWATCH'] = 'Darkly'
	admin = Admin(app, name='blog', template_mode='bootstrap3')
	# User 모델 페이지에 추가
	admin.add_view(ModelView(User, db.session))
{% endhighlight %}

</div>
</details>

그리고 코드를 조금 수정해주어야 한다.

<details open>
<summary>models.py</summary>
<div markdown="1">

{% highlight python linenos %}
# from . import db >> 삭제
from flask_login import UserMixin
from sqlalchemy.sql import func
from flask_sqlalchemy import SQLAlchemy

# __init__.py 에서 가져옴
db = SQLAlchemy()
DB_NAME = "blog_db"
{% endhighlight %}

</div>
</details>

<details open>
<summary>auth.py</summary>
<div markdown="1">

```
# from . import db >> 삭제
from .models import db, get_user_model

User >> get_user_model()로 바꾸기
```

</div>
</details>

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/admin-01.png" alt="">
  <figcaption>실행을 해주면 User 탭에 유저가 보인다.</figcaption>
</figure>


## 카테고리, 게시물 구현

각 게시물은 유저가 작성하고, 카테고리가 있을 것이다.

그리고 카테고리와 유저는 여러 게시물을 가질 수 있으므로 일대다 관계이다.

ORM을 이용해 먼저 게시물 모델을 만들어보자.

{% highlight python linenos %}
class Post(db.Model):
    # id : 유일 키, Integer
    id = db.Column(db.Integer, primary_key=True)
    title = db.Column(db.String(200), nullable=False)
    content = db.Column(db.Text(), nullable=False)
    created_at = db.Column(db.DateTime(timezone=True), default=func.now())
    # user 테이블의 id를 참조
    author_id = db.Column(db.Integer, db.ForeignKey(
        'user.id', ondelete='CASCADE'), nullable=False)
    # 역참조
    user = db.relationship(
        'User', backref=db.backref('posts', cascade='delete'))
    # 역참조, category 컬럼의 id를 참조
    category_id = db.Column(db.Integer, db.ForeignKey(
        'category.id', ondelete='CASCADE'))
    category = db.relationship(
        'Category', backref=db.backref('category', cascade='delete'))
{% endhighlight %}

> `*db.ForeignKey` 와 `db.relationship` ?

[점프 투 플라스크](https://wikidocs.net/81045)를 보면 아래와 같이 나와있다.

```
db.ForeignKey의 두 번째 파라미터 ondelete는 삭제 연동 설정이다.
즉, ondelete='CASCADE'는 질문을 삭제하면 해당 질문에 달린 답변도 함께 삭제된다는 의미이다.
>> 블로그에서는 저자 모델이 삭제되었을 경우 해당 저자가 쓴 포스트가 함께 삭제된다.

db.relationship의 첫 번째 파라미터는 참조할 모델명이고 두 번째 backref 파라미터는 역참조 설정이다.
역참조란 쉽게 말해 질문에서 답변을 거꾸로 참조하는 것을 의미한다.
```

그 다음 카테고리 모델도 작성해주자.

{% highlight python linenos %}
class Category(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    name = db.Column(db.String(150), unique=True)
    
    def __repr__(self):
        return f'<{self.__class__.__name__}(name={self.name})>'
{% endhighlight %}

## HTML 폼 작성

기존에 있었던 `contact.html` 파일을 참고하여 `post_create.html` 파일을 작성해준다.

<details open>
<summary>post_create.html</summary>
<div markdown="1">

{% highlight python linenos %}
{% raw %}{% extends 'base.html' %}{% endraw %}

{% raw %}{% block title %}Create a Post{% endblock %}{% endraw %}

{% raw %}{% block header %}{% endraw %}
    <header class="masthead"
            style="background-image: url('{% raw %}{{ url_for('static', filename='assets/img/create-bg.jpg') }}{% endraw %}'); height: 130px;">
        <div class="container position-relative px-4 px-lg-5">
            <div class="row gx-4 gx-lg-5 justify-content-center">
                <div class="col-md-10 col-lg-8 col-xl-7">
                    <div class="site-heading">
                        <h1>Create a Post</h1>
                        <h2>Post whatever you want!</h2>
                    </div>
                </div>
            </div>
        </div>
    </header>
{% raw %}{% endblock %}{% endraw %}

{% raw %}{% block content %}{% endraw %}
    <!-- Main Content-->
    <main class="mb-4">
        <div class="container px-4 px-lg-5">
            <div class="row gx-4 gx-lg-5 justify-content-center">
                <div class="col-md-10 col-lg-8 col-xl-12">
                    <div class="my-5">
                        <form id="createForm" method="POST">
                            {% raw %}{{ form.csrf_token }}{% endraw %}
                            <div style="margin: 20px;">
                                <input class="form-control" id="title" type="text" placeholder="Post Title"
                                       name="title" style="font-size: 40px;"/>
                            </div>
                            <div style="margin: 20px;">
                                <textarea class="form-control" id="content" type="text" placeholder="Content"
                                          name="content" style="height: 500px;"></textarea>
                            </div>
                            <div style="margin: 20px;" id="category">
                                <select class="form-control" name="category" required>
                                    <option value="" disabled selected>select a category</option>
                                    {% raw %}{% for category in categories %}{% endraw %}
                                        <option value="{{ category.id }}">{% raw %}{{ category.name }}{% endraw %}</option>
                                    {% raw %}{% endfor %}{% endraw %}
                                </select>
                            </div>
                            <br/>
                            <div style="text-align: center">
                                <button class="btn btn-primary text-uppercase" id="submitButton" type="submit">
                                    Create
                                </button>
                            </div>
                        </form>
                    </div>
                </div>
            </div>
        </div>
    </main>
{% raw %}{% endblock %}{% endraw %}
{% endhighlight %}

</div>
</details>

## 테스트 코드 작성

<details open>
<summary>tests.py</summary>
<div markdown="1">

{% highlight python linenos %}
def test_add_category_and_post(self):
    # "python" 카테고리 추가
    self.python_category = get_category_model()(
        name="python"
    )
    db.session.add(self.python_category)
    db.session.commit()
    # 추가한 카테고리의 이름이 "python" 인지 확인
    self.assertEqual(get_category_model().query.first().name, "python")
    # id가 1인지 확인
    self.assertEqual(get_category_model().query.first().id, 1)

    # "rust" 카테고리 추가
    self.rust_category = get_category_model()(
        name="rust"
    )
    db.session.add(self.rust_category)
    db.session.commit()
		# id가 2인 카테고리의 이름이 "rust" 인지 확인
    self.assertEqual(get_category_model().query.filter_by(id=2).first().name,
                     "rust")

    # "javascript" 카테고리 추가
    self.rust_category = get_category_model()(
        name="javascript"
    )
    db.session.add(self.rust_category)
    db.session.commit()

    # 카테고리 리스트 페이지에 접속했을 때
		# 추가했던 3개의 카테고리가 잘 추가되어 있는지 확인
    response = self.client.get('/categories-list')
    soup = BeautifulSoup(response.data, 'html.parser')
    self.assertIn('python', soup.text)
    self.assertIn('rust', soup.text)
    self.assertIn('javascript', soup.text)

    # 로그인 전에는, 포스트 작성 페이지에 접근한다면 로그인 페이지로 이동해야 함
		# 리디렉션을 나타내는 상태 코드는 302이므로 이를 확인
    response = self.client.get('/create-post', follow_redirects=False)
    self.assertEqual(302, response.status_code)

		# 게시물의 작성자 생성
    response = self.client.post('/auth/sign-up',
                                data=dict(email="helloworld@naver.com", username="hello", password1="dkdldpvmvl",
                                          password2="dkdldpvmvl"))

    # 위에서 만든 유저로 로그인
    with self.client:
        response = self.client.post('/auth/login',
                                    data=dict(email="helloworld@naver.com", username="hello",
                                              password="dkdldpvmvl"),
                                    follow_redirects=True)
        # 로그인한 상태로, 게시물 작성 페이지에 갔을 때에 폼이 잘 떠야 한다.
        response = self.client.get('/create-post')
        # 서버에 get 요청을 보냈을 때에, 정상적으로 응답한다는 상태 코드인 200을 돌려주는지 확인
        self.assertEqual(response.status_code, 200)

        # 미리 작성한 카테고리 3개가 셀렉트 박스의 옵션으로 잘 뜨는지 확인
        soup = BeautifulSoup(response.data, 'html.parser')
        select_tags = soup.find(id='category')
        self.assertIn("python", select_tags.text)
        self.assertIn("rust", select_tags.text)
        self.assertIn("javascript", select_tags.text)

        response_post = self.client.post('/create-post',
                                         data=dict(title="안녕하세요, 첫 번째 게시물입니다.",
                                                   content="만나서 반갑습니다!",
                                                   category="1"),
                                         follow_redirects=True)
        # 게시물을 폼에서 작성한 후, 데이터베이스에 남아 있는 게시물의 수가 1개인지 확인
        self.assertEqual(1, get_post_model().query.count())

    # 게시물은 잘 추가되어 있는지 확인
    response = self.client.get(f'/posts/1')
    soup = BeautifulSoup(response.data, 'html.parser')

    # 게시물의 페이지에서 우리가 폼에서 입력했던 제목이 잘 나타나는지 확인
    title_wrapper = soup.find(id='title-wrapper')
    self.assertIn("안녕하세요, 첫 번째 게시물입니다.", title_wrapper.text)

    # 게시물 페이지에서, 로그인했던 유저의 이름이 저자로 잘 표시되는지 확인
    author_wrapper = soup.find(id='author-wrapper')
    self.assertIn("hello", author_wrapper.text)

    db.session.close()
{% endhighlight %}

</div>
</details>

### 카테고리 리스트 페이지에 카테고리가 표시되지 않음

---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/err-01.png" alt="">
</figure>

카테고리 리스트 페이지에 접속하는 것이므로 `views.py/categories_list()`에 카테고리들을 컨텍스트로 넘겨주어야 한다.

그리고, 카테고리 리스트 페이지에서 그 값을 보여주면 된다.

<details open>
<summary>views.py</summary>
<div markdown="1">

{% highlight python linenos %}
from .models import get_category_model
...
@views.route("/categories-list")
def categories_list():
    # 모든 카테고리 가져오기
    categories = get_category_model().query.all()
    return render_template("categories_list.html", user=current_user, categories=categories)
{% endhighlight %}

</div>
</details>

<details open>
<summary>categories-list.html</summary>
<div markdown="1">

{% highlight python linenos %}
{% raw %}{% block content %}{% endraw %}
    <!-- Main Content-->
    <div class="container px-4 px-lg-5">
        <div class="row gx-4 gx-lg-5 justify-content-center">
            <div class="col-md-10 col-lg-8 col-xl-10">
                <!-- Category item-->
                {% raw %}{% for category in categories %}{% endraw %}
                    <!-- Category item-->
                    <div class="post-preview">
                        <a href="post_detail.html">
                            <h2 class="post-title">{% raw %}{{ category.name }}{% endraw %}</h2>
                        </a>
                    </div>
                    <!-- Divider-->
                    <hr class="my-4"/>
                {% raw %}{% endfor %}{% endraw %}
            </div>
        </div>
    </div>
{% raw %}{% endblock %}{% endraw %}
{% endhighlight %}

</div>
</details>


### 응답 상태 코드가 302이 아님

---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/err-02.png" alt="">
</figure>

로그인이 필요하다는 데코레이터를 `create_post()`에 추가해주면 된다.

{% highlight python linenos %}
@views.route("/create-post")
@login_required # 추가
def create_post():
    return render_template("post_create.html", user=current_user, categories=categories)
{% endhighlight %}

### select 태그에 카테고리가 표시되지 않음

---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/err-03.png" alt="">
</figure>

카테고리 리스트 페이지에서처럼 모든 카테고리를 컨텍스트로 넘겨준 다음, 포스트 작성 페이지에서 보여주면 된다.

<details open>
<summary>views.py</summary>
<div markdown="1">

{% highlight python linenos %}
@views.route("/create-post")
@login_required
def create_post():
    # 모든 카테고리 가져오기
    categories = get_category_model().query.all()
    return render_template("post_create.html", user=current_user, categories=categories)
{% endhighlight %}

</div>
</details>

<details open>
<summary>post_create.html</summary>
<div markdown="1">

{% highlight python linenos %}
<select class="form-control" name="category" required>
    <option value="" disabled selected>select a category</option>
    {% raw %}{% for category in categories %}{% endraw %}
        <option value="{{ category.id }}">{% raw %}{{ category.name }}{% endraw %}</option>
    {% raw %}{% endfor %}{% endraw %}
</select>
{% endhighlight %}

</div>
</details>


### 데이터베이스에 값이 저장되지 않음

---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/err-04.png" alt="">
</figure>

이 오류의 경우 다음 챕터에서 자세히 다뤄보겠다.

## 게시물 작성하기

폼을 통해 게시물을 작성하는 것이므로 폼에서 받아온 데이터를 검증한 뒤에 데이터베이스에 저장해주는 과정으로 구현하면 될 것이다.

<details open>
<summary>forms.py</summary>
<div markdown="1">

{% highlight python linenos %}
# 데이터 검증을 쉽게 하기 위함
class PostForm(FlaskForm):
    title = StringField('title', validators=[DataRequired()])
    content = TextAreaField('content', validators=[DataRequired()])
    category = StringField('category', validators=[DataRequired()])

def get_post_model():
    return PostForm
{% endhighlight %}

</div>
</details>

<details open>
<summary>views.py</summary>
<div markdown="1">

{% highlight python linenos %}
from .forms import PostForm

@views.route("/create-post", methods=['GET', 'POST'])
@login_required
def create_post():
    form = PostForm()
		# 폼에서 받아온 검증된 데이터를 데이터베이스에 저장
    if request.method == "POST" and form.validate_on_submit():
        post = get_post_model()(
            title=form.title.data,
            content=form.content.data,
            category_id=form.category.data,
            author_id=current_user.id,
        )
        db.session.add(post)
        db.session.commit()
				# 저장을 완료했다면 홈으로 리다이렉트
        return redirect(url_for("views.home"))
		# 카테고리를 컨텍스트로 넘겨줌
    else:
        # 모든 카테고리 가져오기
        categories = get_category_model().query.all()
        return render_template("post_create.html", form=form, user=current_user, categories=categories)
{% endhighlight %}

</div>
</details>

추가로 앞서 나왔던 폼들과 같이 `{{ form.csrf_token }}` 을 추가해주자.

## 게시물 보여주기

이전에 만들어두었던 `post_detail()`을 수정해보자.

url에서 id를 받아와 (/posts/1, id=1) 해당 id의 게시물을 찾아서 정보들을 모두 넘겨주면 된다.

<details open>
<summary>views.py</summary>
<div markdown="1">

{% highlight python linenos %}
@views.route('/posts/<int:id>')
def post_detail(id):
    post = get_post_model().query.filter_by(id=id).first()
    return render_template("post_detail.html", user=current_user, post=post)
{% endhighlight %}

</div>
</details>

넘겨 받은 데이터를 화면에 보여주도록 html 파일을 수정해주자.

<details open>
<summary>post_detail.html</summary>
<div markdown="1">

{% highlight python linenos %}
<div class="post-heading">
    <h1>{% raw %}{{ post.title }}{% endraw %}</h1>
    <span class="meta">
        <div id="author-wrapper"><p>Posted by <a href="#!">저자명</a></p></div>
        <p>on {% raw %}{{ post.created_at }}{% endraw %}</p>
    </span>
</div>
{% endhighlight %}

</div>
</details>

저자명은 어떻게 보여주어야 할까?

받아온 데이터에는 저자의 id 값만 들어있다.

우리가 원하는 데이터는 저자의 유저 네임이기 때문에 User 모델을 참조해야 한다.

앞에서 Post 모델에 `db.relationship(… backref(…))`라는 코드를 이미 작성해놨기에 `post.user`로 User 모델에 쉽게 접근할 수 있다.

따라서 저자명 부분에 `{{ post.user.username }}`를 작성해주자.

### 관리자 페이지에 모델 등록

---

```python
admin.add_view(ModelView(get_post_model(), db.session))
admin.add_view(ModelView(get_category_model(), db.session))
```

## 서버 실행 후 테스트

이제 테스트 코드 말고 실제로 확인을 해보자.

관리자 페이지에서 카테고리를 생성하고,

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/catest-01.png" alt="">
</figure>

카테고리 리스트 페이지에 들어가보면 잘 추가된 것을 확인할 수 있다.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/catest-02.png" alt="">
</figure>

포스트를 작성하고,

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/catest-03.png" alt="">
</figure>

/posts/1로 접속을 한다면 포스트 내용이 잘 보인다.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/catest-04.png" alt="">
</figure>

추가로 /create-post로 접속해 폼으로 포스트 작성 후, /posts/2에 접속해도

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/catest-05.png" alt="">
</figure>

잘 보이는 것을 확인할 수 있다.


## 카테고리 페이지에 포스트 리스트 보여주기

카테고리 리스트 페이지에서 특정 카테고리(예: TodayILearned)를 클릭하면 해당하는 게시물들을 보여주도록 구현해보자.

먼저 카테고리 id를 url에 입력 받을 수 있게 해주었다.

{% highlight python linenos %}
@views.route("/post-list/<int:id>")
def post_list():
    return render_template("post_list.html", user=current_user)
{% endhighlight %}

그리고 카테고리 이름을 누르면 카테고리 id에 해당하는 post-list 페이지로 갈 수 있게 템플릿 파일을 수정해주었다.

<details open>
<summary>categories_list.html</summary>
<div markdown="1">

{% highlight python linenos %}
{% raw %}{% for category in categories %}{% endraw %}
    <!-- Category item-->
    <div class="post-preview">
        <a href="{% raw %}{{ url_for('views.post_list', id=category.id) }}{% endraw %}">
            <h2 class="post-title">{% raw %}{{ category.name }}{% endraw %}</h2>
        </a>
    </div>
    <!-- Divider-->
    <hr class="my-4"/>
{% raw %}{% endfor %}{% endraw %}
{% endhighlight %}

</div>
</details>

포스트 리스트 페이지 템플릿에 현재 카테고리 정보를 전달해주면 된다.

{% highlight python linenos %}
@views.route("/post-list/<int:id>")
def post_list(id):
    current_category = get_category_model().query.filter_by(id=id).first()
    posts = get_post_model().query.filter_by(category_id=id)
    return render_template("post_list.html", user=current_user, posts=posts, current_category=current_category)
{% endhighlight %}

### 포스트 리스트 페이지에 포스트 보여주기

---

카테고리 이름과 몇 개의 포스트가 있는지 표시하고 싶다.

{% highlight python linenos %}
<div class="site-heading">
    <h1>{% raw %}{{ current_category.name }}{% endraw %}</h1>
    <span class="subheading">({% raw %}{{ posts.count() }}{% endraw %}) 개의 포스트가 있습니다.}</span>
</div>
{% endhighlight %}

위와 같이 작성해주고 TodayILearned 카테고리를 클릭한다면…

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/catest-06.png" alt="">
  <figcaption>제대로 뜬다.</figcaption>
</figure>

이제 포스트를 보여줘보자. for문을 이용해 하나하나 보여줄 수 있겠다.

{% highlight python linenos %}
<!-- Post preview-->
{% raw %}{% for post in posts %}{% endraw %}
    <div class="post-preview">
        <a href="{% raw %}{{ url_for('views.post_detail', id=post.id) }}{% endraw %}">
            <h6 class="post-title">{% raw %}{{ post.title }}{% endraw %}</h6>
        </a>
        <p class="post-meta">
            Posted by
            <a href="#!">{% raw %}{{ post.user.username }}{% endraw %}</a>
            on {% raw %}{{ post.created_at }}{% endraw %}
        </p>
    </div>
{% raw %}{% endfor %}{% endraw %}
<!-- Divider-->
<hr class="my-4"/>
{% endhighlight %}

작성하고 실행해보면…

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/catest-07.png" alt="">
  <figcaption>잘 나온다.</figcaption>
</figure>


## 스태프 권한 보유자만 게시물 작성 권한 주기

테스트 코드로 한 번 테스트 해보자.

전에 썼던 TestPostwithCategory 클래스를 수정해주면 된다.

코드의 경우, [이 곳](https://github.com/overtae/ifp-flask-study/blob/05-blog-admin/tests/tests.py)을 참고하면 좋다.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/catest-08.png" alt="">
  <figcaption>staff 권한이 있을 때만 접근이 가능해야하지만, 없을 때도 가능하다는 실패</figcaption>
</figure>

위 실패는 `create_post()`에 조건문을 추가하면 해결할 수 있다.

{% highlight python linenos %}
@views.route("/create-post", methods=['GET', 'POST'])
@login_required
def create_post():
    if current_user.is_staff == True:
        form = PostForm()
        if request.method == "POST" and form.validate_on_submit():
            post = get_post_model()(
                title=form.title.data,
                content=form.content.data,
                category_id=form.category.data,
                author_id=current_user.id,
            )
            db.session.add(post)
            db.session.commit()
            return redirect(url_for("views.home"))
        else:
            # 모든 카테고리 가져오기
            categories = get_category_model().query.all()
            return render_template("post_create.html", form=form, user=current_user, categories=categories)
    else:
				# 403 에러 발생
        return abort(403)
{% endhighlight %}

## 게시물 수정, 삭제

먼저 테스트 코드 작성 후 실행을 해보자.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/catest-09.png" alt="">
  <figcaption>Edit 버튼이 없다는 것 같다.</figcaption>
</figure>

포스트 디테일 템플릿에 버튼을 추가해주도록 하자.

<details open>
<summary>post_detail.html</summary>
<div markdown="1">

{% highlight python linenos %}
<button>
    {% raw %}<a href="{{ url_for("views.edit_post", id=post.id) }}">Edit</a>{% endraw %}
</button>
{% endhighlight %}

</div>
</details>

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/catest-10.png" alt="">
  <figcaption>라우팅에 추가하지 않은 함수를 url_for에 사용하여 실패했다.</figcaption>
</figure>

post_create.html 템플릿을 복사하여 post_edit.html 파일을 만들어준다.

폼에 원래 작성했었던 항목들을 보여주게끔 수정해주었다.

{% highlight python linenos %}
<form id="createForm" method="POST">
    {% raw %}{{ form.csrf_token }}{% endraw %}
    <div style="margin: 20px;">
        <input class="form-control" value="{% raw %}{{ post.title }}{% endraw %}" id="title" type="text" placeholder="Post Title"
               name="title" style="font-size: 40px;"/>
    </div>
    <div style="margin: 20px;">
        <textarea class="form-control" id="content" type="text" placeholder="Content"
                  name="content" style="height: 500px;">{% raw %}{{ post.content }}</textarea>
    </div>
    <div style="margin: 20px;" id="category">
        <select class="form-control" name="category" required>
            <option value="" disabled>select a category</option>
            {% raw %}{% for category in categories %}{% endraw %}
                {% raw %}{% if post.category == category %}{% endraw %}
                    {% raw %}<option value="{{ category.id }}" selected>{{ category.name }}</option>{% endraw %}
                {% raw %}{% else %}{% endraw %}
                    {% raw %}<option value="{{ category.id }}">{{ category.name }}</option>{% endraw %}
                {% raw %}{% endif %}{% endraw %}
            {% raw %}{% endfor %}{% endraw %}
        </select>
    </div>
    <br/>
    <div style="text-align: center">
        <button class="btn btn-primary text-uppercase" id="submitButton" type="submit">
            Edit
        </button>
    </div>
</form>
{% endhighlight %}

그리고 폼에서 받아온 데이터들로 포스트를 수정할 수 있게 하였다.

{% highlight python linenos %}
@views.route("/edit-post/<int:id>", methods=["GET", "POST"])
@login_required
def edit_post(id):
    post = get_post_model().query.filter_by(id=id).first()
    form = PostForm()
    categories = get_category_model().query.all()

    if current_user.is_staff == True and current_user.username == post.user.username:
        if request.method == "GET":
            # 원래 게시물 내용
            return render_template("post_edit.html", user=current_user, post=post, categories=categories, form=form)
        elif request.method == "POST" and form.validate_on_submit():
            # 수정 작업 완료 코드
            post.title = form.title.data
            post.content = form.content.data
            post.category_id = int(form.category.data)
            db.session.commit()
            return redirect(url_for("views.home"))
    else:
        abort(403)
{% endhighlight %}

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/catest-11.png" alt="">
  <figcaption>작성자가 아닌 사람이 접속했는데도 Edit 버튼이 보여 실패했다.</figcaption>
</figure>

간단히 if문으로 감싸주면 해결된다.

<details open>
<summary>post_detail.html</summary>
<div markdown="1">

{% highlight python linenos %}
{% raw %}{% if post.user.username == current_user.username %}{% endraw %}
<button class="btn btn-info" id="edit-button">
    <a href="{% raw %}{{ url_for("views.edit_post", id=post.id) }}{% endraw %}">Edit</a>
</button>
{% raw %}{% endif %}{% endraw %}
{% endhighlight %}

</div>
</details>


## 관리자 페이지 접근 권한

관리자 페이지는 이름 그대로 관리자만 접근이 가능해야 하기에, ModelView를 상속 받은 MyAdminView 클래스를 만들어 로그인했고, 스태프 권한이 있는 유저만 접근이 가능하도록 해주자.

{% highlight python linenos %}
# 관리자 페이지
app.config['FLASK_ADMIN_SWATCH'] = 'Darkly'
admin = Admin(app, name='blog', template_mode='bootstrap3')

class MyAdminView(ModelView):
    def is_accessible(self):
        return current_user.is_authenticated and current_user.is_staff == True

    def inaccessible_callback(self, name, **kwargs):
        return abort(403)

admin.add_view(MyAdminView(get_user_model(), db.session))
admin.add_view(MyAdminView(get_post_model(), db.session))
admin.add_view(MyAdminView(get_category_model(), db.session))
{% endhighlight %}

이제 스태프 권한이 없는 유저가 접근하려고 한다면 아래와 같은 메시지가 나올것이다.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/adtest-01.png" alt="">
</figure>


## 관리자 페이지 커스텀

관리자 페이지에서 유저를 생성하면 비밀번호가 그대로 보이는 것을 확인할 수 있다.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/adtest-02.png" alt="">
  <figcaption>문제 없이 성공했다.이렇게 보이면 스태프 권한이 있다면 해당 유저로 누구나 로그인이 가능하다…</figcaption>
</figure>

이렇게 보이면 스태프 권한이 있다면 해당 유저로 누구나 로그인이 가능하다…

아래의 코드를 작성하여 관리자 페이지에서 유저를 등록해도 비밀번호 해싱이 되게끔 해주자.

{% highlight python linenos %}
class MyUserView(ModelView):
    def is_accessible(self):
        if current_user.is_authenticated and current_user.is_staff == True:
            return True
        else:
            return abort(403)

    class CustomPasswordField(StringField):
        def populate_obj(self, obj, name):
            setattr(obj, name, generate_password_hash(self.data))

    form_extra_fields = {
        'password': CustomPasswordField('Password', validators=[InputRequired()])
    }
    form_excluded_columns = {
        'posts', 'created_at'
    }
{% endhighlight %}

작성해주고 유저를 새로 등록한다면..!

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/adtest-03.png" alt="">
  <figcaption>해싱이 잘 됐다!!</figcaption>
</figure>


### 추가로…

---

Post 탭과 CategoryView 탭도 접근 권한을 설정해주기 위해 is_accessible 메소드를 오버라이드해주고, form_excluded_columns 를 이용하여 원치 않는 필드를 관리자 페이지에서 생성하지 않도록 해주자.

{% highlight python linenos %}
class MyPostView(ModelView):
    def is_accessible(self):
        if current_user.is_authenticated and current_user.is_staff == True:
            return True
        else:
            return abort(403)

    form_excluded_columns = {
        'created_at', 'comments'
    }

class MyCategoryView(ModelView):
    def is_accessible(self):
        if current_user.is_authenticated and current_user.is_staff == True:
            return True
        else:
            return abort(403)

    form_excluded_columns = {
        'category'
    }
{% endhighlight %}

작성해주고, 아래와 같이 기존 `MyAdminView(…)` 을 각 클래스로 바꿔주면 된다.

```python
admin.add_view(MyUserView(get_user_model(), db.session))
admin.add_view(MyPostView(get_post_model(), db.session))
admin.add_view(MyCategoryView(get_category_model(), db.session))
```

**Notice:** 사용한 코드는 [이 곳](https://github.com/overtae/ifp-flask-study/tree/05-blog-admin)에서 확인가능합니다.
{: .notice--info}