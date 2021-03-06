---
title: "[파이썬 플라스크] 블로그 웹 애플리케이션 개발 - 회원가입, 로그인"
date: 2022-07-28
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "회원가입과 로그인 기능 처리"
---

## 개요

저번에 설치했던 `Flask-SQLAlchemy`와 `Flask-Login`을 로그인과 회원가입을 구현해보며 사용해보도록 하겠다.

회원가입과 로그인시 사용자가 입력한 정보들을 토대로 서버에 해당 사용자의 데이터가 존재하는지 확인을 하는 과정이 필요하다.

따라서 폼에 입력한 데이터를 POST 방식으로 받아와 데이터를 **저장**(회원가입)하거나 **확인**(로그인 또는 중복 데이터)해주어야 한다.

## form method = "POST"

회원가입과 로그인 템플릿 파일을 살펴보면

```html
<form id="contactForm" method="POST">
```

위와 같은 코드를 확인할 수 있다.

폼에 아무 값이나 입력하고 버튼을 누르면,

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/signup-no.png" alt="">
  <figcaption>POST 요청인 것을 확인 가능하다.</figcaption>
</figure>

따라서 singup과 login에 POST 요청을 처리하는 코드를 작성해주어야 한다.

<details open>
<summary>auth.py</summary>
<div markdown="1">

{% highlight python linenos %}
@auth.route("/login", methods=['GET', 'POST'])  # 로그인에서 POST 요청을 처리해야 함.
def login():
    return render_template("login.html")

...

@auth.route("/sign-up", methods=['GET', 'POST'])  # 회원가입에서 POST 요청을 처리해야 함.
def signup():
    return render_template("signup.html")
{% endhighlight %}

</div>
</details>

위 코드를 추가한다면,

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/signup-yes.png" alt="">
  <figcaption>붉은색이었던 요청이 흰색으로 바뀌며 응답 상태가 200인 것을 알 수 있다.</figcaption>
</figure>


## 폼에서 데이터 받아오기: request

`form`의 `input` 태그에는 `name`이라는 속성이 있다.

`name`의 경우 submit 된 데이터를 참조할 때 사용한다.

그 역할을 플라스크에선 `request`가 해준다.

<details open>
<summary>auth.py</summary>
<div markdown="1">

{% highlight python linenos %}
@auth.route("/sign-up", methods=['GET', 'POST'])
def signup():
	email = request.form.get('email')
    print(email)

    username = request.form.get('username')
    print(username)

    password1 = request.form.get('password1')
    print(password1)

    password2 = request.form.get('password2')
    print(password2)
    return render_template("signup.html")
{% endhighlight %}

</div>
</details>

`request.form.get('name 속성')`을 사용해 데이터를 받아올 수 있다.

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/request.png" alt="">
  <figcaption>터미널로 출력 결과를 확인할 수 있다.</figcaption>
</figure>

## 데이터베이스 처리: Flask-SQLAlchemy

이전 포스팅에서 SQLAlcehmy라는 ORM을 사용할 것이라고 설명했었다.

ORM은 클래스를 데이터베이스 테이블로 만들어주기 때문에 유저라는 클래스를 만들어주어야 하고 데이터를 받아와 저장해 둘 데이터베이스가 존재해야 한다.

### 데이터베이스 설정

---

아래 코드를 작성하여 데이터베이스 설정을 해주자.

<details open>
<summary>__init__.py</summary>
<div markdown="1">

{% highlight python linenos %}
# DB 설정하기
db = SQLAlchemy()
DB_NAME =  "blog_db"

...

def create_app():
    
    ...

    # DB 설정하기
    app.config['SQLALCHEMY_DATABASE_URI'] = f'sqlite:///{DB_NAME}'
    db.init_app(app)


# DB 추가하기
def create_database(app):
    if not path.exists("blog/" + DB_NAME):  # DB 경로가 존재하지 않는다면,
        db.create_all(app=app)  # DB를 하나 생성
{% endhighlight %}

</div>
</details>

### 데이터 모델 생성

---

데이터베이스에 직접 SQL 질의를 날리는 대신, 파이썬 클래스로 테이블을 만들어준다.

<details open>
<summary>models.py</summary>
<div markdown="1">

{% highlight python linenos %}
# 데이터베이스 모델에 관한 것을 정의

from . import db
from flask_login import UserMixin
from sqlalchemy.sql import func


class User(db.Model, UserMixin):
    id = db.Column(db.Integer, primary_key=True)      # id : 유일 키, Integer
    # email : 같은 이메일을 가지고 있는 유저가 없도록 함, String
    email = db.Column(db.String(150), unique=True)
    # username : 같은 이름을 가지고 있는 유저가 없도록 함, String
    username = db.Column(db.String(150), unique=True)
    # password : 비밀번호, String
    password = db.Column(db.String(150))
    # 생성일자, 기본적으로 현재가 저장되도록 함
    created_at = db.Column(db.DateTime(timezone=True), default=func.now())
{% endhighlight %}

</div>
</details>

이렇게 만들어준 테이블을 등록해주어야한다.

등록해주지 않을 경우 데이터베이스에 테이블이 생성되지 않으므로 주의하자.

<details open>
<summary>__init__.py에 create_app()</summary>
<div markdown="1">

{% highlight python linenos %}
# DB 생성하는 함수 호출하기
from .models import User
create_database(app)
{% endhighlight %}

</div>
</details>

## 회원가입 기능 구현

회원가입의 과정은 아래와 같다고 할 수 있다.

```
1. 유저가 signup 페이지에서 폼 작성
2. 이메일 중복 검사
3. 유저네임 중복 검사
4. 비밀번호 길이 확인(보안)
5. 나머지 데이터가 유효한지 검사
6. 데이터가 유효할 경우 > 데이터베이스에 유저 생성
```

데이터가 유효한지 검사하기 위해 아래 라이브러리들을 사용해줄 것이다.

```bash
pip install flask-wtf
pip install email-validator
```

설치한 후, `forms.py` 파일을 생성하고 아래의 코드를 작성해주자.

<details open>
<summary>forms.py</summary>
<div markdown="1">

{% highlight python linenos %}
from flask_wtf import FlaskForm
from wtforms import StringField, TextAreaField, PasswordField, EmailField
from wtforms.validators import DataRequired, Email, Length, EqualTo


class SignupForm(FlaskForm):
    # email : 필수 입력 항목이며, 이메일의 형식을 유지해야 함.
    email = EmailField('email', validators=[DataRequired(), Email()])
    # username : 필수 입력 항목이며, 최소 5글자부터 최대 30글자까지 허용됨.
    username = StringField('username', validators=[
                           DataRequired(), Length(4, 30)])
    # password1 : 필수 입력 항목이며, 최소 8글자부터 최대 30글자까지 허용됨, password2와 값이 같아야 함.
    password1 = PasswordField('password', validators=[DataRequired(), Length(
        8, 30), EqualTo("password2", message="Password must match...")])
    password2 = PasswordField('password again', validators=[DataRequired()])


class LoginForm(FlaskForm):
    email = EmailField('email', validators=[DataRequired(), Email()])
    password = PasswordField('password', validators=[DataRequired()])
{% endhighlight %}

</div>
</details>

추가로 `auth.py` 파일의 수정도 필요하다.

<details open>
<summary>auth.py</summary>
<div markdown="1">

{% highlight python linenos %}
@auth.route("/sign-up", methods=['GET', 'POST'])
def signup():
    form = SignupForm()
    if request.method == "POST" and form.validate_on_submit():
        # 폼으로부터 검증된 데이터 받아오기
        signup_user = User(
            email=form.email.data,
            username=form.username.data,
            password=form.password1.data,
        )

        # 폼에서 받아온 데이터가 데이터베이스에 이미 존재하는지 확인
        email_exists = User.query.filter_by(email=form.email.data).first()
        username_exists = User.query.filter_by(username=form.username.data).first()

        # 이메일 중복 검사
        if email_exists:
            flash('Email is already in use...', category='error')
        # 유저네임 중복 검사
        elif username_exists:
            flash('Username is already in use...', category='error')
        # 위의 모든 과정을 통과한다면, 폼에서 받아온 데이터를 새로운 유저로서 저장
        else:
            db.session.add(signup_user)
            db.session.commit()
            flash("User created!!!")
            return redirect(url_for("views.home")) # 저장이 완료된 후 home으로 리다이렉트
    # GET요청을 보낸다면 회원가입 템플릿을 보여줌
    return render_template("signup.html", form=form)
{% endhighlight %}

</div>
</details>

마지막으로, signup 폼 코드 아래에 `{% raw %}{{ form.csrf_token }}{% endraw %}`을 입력해주어야 한다.

```html
<form id="contactForm" method="POST">
    {% raw %}{{ form.csrf_token }}{% endraw %}
    <div class="form-floating">
```

### 비밀번호 해싱 Hashing

---

데이터베이스에는 유저의 아이디, 비밀번호, ... 등등이 저장될 것이다.

이 말은 데이터베이스에 접근이 가능하다면 누구나 해당 유저의 정보를 열람할 수 있다는 것이다.

따라서 비밀번호를 암호화하여 저장하는 **해싱**을 사용할 것이다.

<details open>
<summary>auth.py</summary>
<div markdown="1">

{% highlight python linenos %}
# import 필요
from werkzeug.security import generate_password_hash, check_password_hash

@auth.route("/sign-up", methods=['GET', 'POST'])
def signup():
    form = SignupForm()
    if request.method == "POST" and form.validate_on_submit():
        # 폼으로부터 검증된 데이터 받아오기
        signup_user = User(
            email=form.email.data,
            username=form.username.data,
            # password=form.password1.data, 이 부분을
            password=generate_password_hash(form.password1.data), # 이렇게
        )
{% endhighlight %}

</div>
</details>

## 로그인 기능 구현

로그인 구현 전에, 로그인이 필요한 곳에 로그인하지 않은 유저가 접근할 수 있다면 문제가 많을 것이다.

따라서 `create_app()` 함수 내부에 아래의 코드를 작성해주자.

<details open>
<summary>__init__.py</summary>
<div markdown="1">

{% highlight python linenos %}
login_manager = LoginManager() # LoginManager() 객체 생성
# 만약 로그인이 필요한 곳에 로그인 하지 않은 유저가 접근한 경우, 로그인 페이지로 리다이렉트 되도록 해줌
login_manager.login_view = "auth.login"
login_manager.init_app(app)

# 받은 id로부터, DB에 있는 유저 테이블의 정보에 접근하도록 해 줌
# login manager는 유저 테이블의 정보에 접근해 저장된 세션을 바탕으로 로그인되어 있다면 로그인 페이지로 안 가도 되게끔 해 줌
@login_manager.user_loader
def load_user_by_id(id):
    return User.query.get(int(id))
{% endhighlight %}

</div>
</details>

이제 로그인 기능을 구현해보자.

`forms.py` 파일에 로그인 폼 코드를 작성해주고,

<details open>
<summary>forms.py</summary>
<div markdown="1">

{% highlight python linenos %}
class LoginForm(FlaskForm):
    email = EmailField('email', validators=[DataRequired(), Email()])
    password = PasswordField('password', validators=[DataRequired()])
{% endhighlight %}

</div>
</details>

`auth.py` 파일에 아래와 같이 작성해준다.

<details open>
<summary>auth.py</summary>
<div markdown="1">

{% highlight python linenos %}
@auth.route('/login', methods=['GET', 'POST'])
def login():
    form = LoginForm()
    if request.method == "POST" and form.validate_on_submit():

        # 폼으로부터 검증된 데이터 받아오기
        password = form.password.data

        # 폼에서 받아온 이메일로 유저 찾기
        user = User.query.filter_by(email=form.email.data).first()

        # 로그인 폼에서 입력된 이메일이 존재한다면
        if user:
            if check_password_hash(user.password, password):
                flash("로그인하였습니다.", category='success')
                login_user(user, remember=True)
                return redirect(url_for('views.home'))
            else:
                flash("Password is incorrect!", category='error')
        # 로그인 폼에서 입력된 이메일이 존재하지 않는다면
        else:
            flash("Email does not exist...", category='error')

    return render_template("login.html", form=form, user=current_user)
{% endhighlight %}

</div>
</details>

회원가입과 동일하게 로그인 템플릿 파일에 `{% raw %}{{ form.csrf_token }}{% endraw %}`을 추가해주는 것을 잊지 말자.

```html
<form id="contactForm" method="POST">
    {% raw %}{{ form.csrf_token }}{% endraw %}
    <div class="form-floating">
```

## 로그아웃 기능 구현

로그아웃의 경우 `flask-login`을 사용하여 쉽게 처리가 가능하다.

다만 로그인이 되어 있어야 로그아웃이 된다는 점을 염두해두어야 한다.

<details open>
<summary>auth.py</summary>
<div markdown="1">

{% highlight python linenos %}
@auth.route('/logout')
@login_required # 로그인 필요
def logout():
    logout_user()
    # 로그아웃하면 views의 home으로 리다이렉트됨
    return redirect(url_for('views.home'))
{% endhighlight %}

</div>
</details>

## 오류 메시지 출력

만약 유효하지 않은 이메일이나 유저네임을 입력했다면 오류 메시지가 나오면 좋을 것이다.

우리는 로그인 코드에서 이미 메시지를 입력해놓았다.

{% highlight python linenos %}
if user:
    if check_password_hash(user.password, password):
        flash("Logged in!", category='success') # 메시지
        login_user(user, remember=True)
        return redirect(url_for('views.home'))
    else:
        flash("Password is incorrect!", category='error') # 메시지
else:
    flash("Email does not exist...", category='error') # 메시지
{% endhighlight %}

위에 작성한 메시지들을 이용해 예쁘게 보여주기만 하면 된다.

<details open>
<summary>base.html</summary>
<div markdown="1">

header 아래에 코드를 작성해주자.

{% highlight python linenos %}
{% raw %}{% with messages = get_flashed_messages(with_categories=True) %}{% endraw %}
    {% raw %}{% if messages %}{% endraw %}
        {% raw %}{% for category, message in messages %}{% endraw %}
            {% raw %}{# 카테고리 == error 이라면, 실패 메시지를 출력 #}{% endraw %}
            {% raw %}{% if category == "error" %}{% endraw %}
                <div class="alert alert-danger alert-dismissable fade show" role="alert" style="text-align: center">
                    {% raw %}{{ message }}{% endraw %}
                    <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
                </div>
                {% raw %}{# 그렇지 않다면, 성공 메시지를 출력 #}{% endraw %}
            {% raw %}{% else %}{% endraw %}
                <div class="alert alert-success alert-dismissable fade show" role="alert" style="text-align: center">
                    {% raw %}{{ message }}{% endraw %}
                    <button type="button" class="btn-close" data-bs-dismiss="alert"></button>
                </div>
            {% raw %}{% endif %}{% endraw %}
        {% raw %}{% endfor %}{% endraw %}
    {% raw %}{% endif %}{% endraw %}
{% raw %}{% endwith %}{% endraw %}
{% endhighlight %}

</div>
</details>

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/msg.png" alt="">
  <figcaption>헤더 아래에 success 메시지가 출력되었다.</figcaption>
</figure>

## 동적으로 변하는 navbar

동적인 navbar의 경우에도 유저 네임을 전달받아 if문을 사용해 구현할 수 있겠다.

유저 네임 전달의 경우 return 문에 `user=current_user`을 추가해준다.

```python
# import 필요
from flask_login import current_user

# auth.py : login(), signup()
return render_template("~~.html", form=form, user=current_user)

# view.py : all
return render_template("~~.html", user=current_user)
```

전달해주었다면 템플릿을 수정해주도록 하자.

<details open>
<summary>base.html</summary>
<div markdown="1">

{% highlight python linenos %}
<div class="collapse navbar-collapse" id="navbarResponsive">
    <ul class="navbar-nav ms-auto py-4 py-lg-0">
    <li class="nav-item"><a class="nav-link px-lg-3 py-3 py-lg-4" href="{% raw %}{{ url_for('views.home') }}{% endraw %}">Home</a></li>
    <li class="nav-item"><a class="nav-link px-lg-3 py-3 py-lg-4" href="{% raw %}{{ url_for('views.about') }}{% endraw %}">About</a></li>
    <li class="nav-item"><a class="nav-link px-lg-3 py-3 py-lg-4" href="{% raw %}{{ url_for('views.categories_list') }}{% endraw %}">Categories</a></li>
    <li class="nav-item"><a class="nav-link px-lg-3 py-3 py-lg-4" href="{% raw %}{{ url_for('views.contact') }}{% endraw %}">Contact</a></li>
    
    {% raw %}{% if user.is_authenticated %}{% endraw %}
    {% raw %}{# 유저가 로그인했다면 보일 것들 : "Welcome, username", "Logout" 이고, user 정보가 넘어가야 함. #}{% endraw %}
    <li class="nav-item"><a class="nav-link px-lg-3 py-3 py-lg-4" style="color: red"
                    href="#">welcome, {% raw %}{{ user.username }}{% endraw %}!</a></li>
    <li class="nav-item"><a class="nav-link px-lg-3 py-3 py-lg-4" style="color: red"
                    href="{% raw %}{{ url_for('auth.logout') }}{% endraw %}">Logout</a></li>
    {% raw %}{% endif %}{% endraw %}

    {% raw %}{% if not user.is_authenticated %}{% endraw %}
    {% raw %}{# 그리고, 로그인한 상태가 아니라면... #}{% endraw %}
    <li class="nav-item"><a class="nav-link px-lg-3 py-3 py-lg-4"
                            href="{% raw %}{{ url_for('auth.signup') }}{% endraw %}">Sign Up</a></li>
    <li class="nav-item"><a class="nav-link px-lg-3 py-3 py-lg-4"
                            href="{% raw %}{{ url_for('auth.login') }}{% endraw %}">Login</a></li>
    {% raw %}{% endif %}{% endraw %}
    
    </ul>
</div>
{% endhighlight %}

</div>
</details>

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/navbar.png" alt="">
  <figcaption>잘 바뀌었다.</figcaption>
</figure>

**Notice:** 사용한 코드는 [이 곳](https://github.com/overtae/ifp-flask-study/tree/04-blog-login)에서 확인가능합니다.
{: .notice--info}