---
title: "[파이썬 플라스크] 블로그 웹 애플리케이션 개발 - 개발 환경 구축"
date: 2022-07-14
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "ORM, 블루프린트, jinja 템플릿 엔진, render_template"
---

## 개요

이 프로젝트에서 우리는 ORM과 로그인 기능을 사용하기 위해 두 가지의 라이브러리를 이용할 것이다. 먼저 ORM이 무엇인지 간단하게 짚고 넘어가보겠다.

## ORM(Object Relational Mapping)

객체와 관계형 데이터베이스의 데이터를 자동으로 매핑(연결)해주는 것을 말한다.

> 그렇다면 왜 ORM을 사용하는 것일까?

객체지향 프로그래밍은 클래스를 사용하고 관계형 데이터베이스는 테이블을 사용하기 때문에 객체 모델과 관계형 모델 간에 불일치가 존재한다.

이때, ORM을 통해 객체 간의 관계를 바탕으로 SQL을 자동으로 생성하여 불일치를 해결하는 것이다.

다시 말해 **SQL 질의어의 사용 없이, 객체를 통해 간접적으로 데이터베이스 데이터를 다룬다**는 것이다.

## Flask-SQLAlchemy

파이썬에서 ORM으로 많이 쓰이는 것들 중 여러 일반적인 관계형 데이터베이스(mysql, sqlite, oracle, …)를 지원하는 SQLAlchemy Tool-kit이 있다.

이 프로젝트에서는 SQLAlchemy를 사용할 것이기에, Flask에서 플러그인처럼 사용하기 쉽게 만들어 놓은 Flask-SQLALchemy를 설치해줄것이다.

아래의 명령어를 입력해주기 전에,

```bash
pip install Flask-SQLAlchemy
```

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/venv.PNG" alt="">
  <figcaption>위와 같이 (venv) 가상환경이 활성화되어있는지 확인해주도록 하자.</figcaption>
</figure>


## Flask-Login

Flask-Login은 Flask에서 로그인 기능을 도와주는 라이브러리로 **사용자 정보를 세션에 저장**한다.

설치를 위해 아래의 명령어를 입력해주자.

이번에도 역시 가상환경이 활성화되어있는지 확인해주어야 한다.

```bash
pip install Flask-Login
```

## `__init__.py`

먼저 blog 폴더에 `__init__.py` 파일을 생성해주자.

`__init__.py` 는 무슨 파일일까? [Python 문서](https://docs.python.org/3/tutorial/modules.html#packages) 를 확인해보면…

```
`__init__.py` 파일은 파일이 포함된 디렉터리를 패키지로 다루는 데 필요하다.
이렇게 할 경우 string과 같이 일반적인 이름을 가진 디렉토리(유효한 모듈)가
나중에 모듈 검색 경로에서 의도하지 않게 숨겨지는 것을 방지할 수 있다.
가장 간단한 상황에서는 `__init__.py` 가 비어있을 수 있지만,
패키지에 대한 초기화 코드를 실행하거나 `__all__` 변수를 설정할 수도 있다.
``` 

정리해보자면, `__init__.py`는 하위 디렉토리의 파일들을 전체적으로 초기화하고 실행하는 파일이다.

그럼 이제 파일을 작성해보자.

<details open>
<summary>__init__.py</summary>
<div markdown="1">

{% highlight python linenos %}
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from os import path
from flask_login import LoginManager

# app을 만들어주는 함수 지정
def create_app():
    app = Flask(__name__)  # Flask app 만들기
    app.config['SECRET_KEY'] = 'IFP'

    @app.route("/")
    def home():
        return "This Is Home"

    @app.route("/about_me/")
    def about_me():
        return "Introduce About Me..."

    return app  # app 반환
{% endhighlight %}

</div>
</details>

<details open>
<summary>app.py</summary>
<div markdown="1">

{% highlight python linenos %}
from blog import create_app
# __init__.py 에 작성한 create_app 함수를 app.py에 불러와

if __name__ == "__main__":
    app = create_app() # app.py에서 사용
    app.run(debug=True)
{% endhighlight %}

</div>
</details>

`app.py`에 if문에 따라 `__init__.py` 파일을 실행시킬때의 이름은 `__main__`이 된다.

그리고 `python ./app.py` 로 앱을 실행 실행해본다면…

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/init.png" alt="">
  <figcaption>폴더 이름인 blog로 실행이 되는 것을 확인할 수 있다.</figcaption>
</figure>


## Blueprint

보통 웹사이트 접속시 `/blog/about` 이나 `/blog/category` 와 같은 경로를 갖는다.

`@app.route("/blog")`, `@views.route("/blog/about")` 등...

모든 함수를 전부 `create_app()` 에 적어준다면 길이도 길어지고 엄청 복잡해질 것이다.

플라스크에선 이를 해결하기 위해 Blueprint 라는 것을 활용한다.

### 파일 나누기

`__init__.py` 와 같은 레벨에 `views.py`, `auth.py` 파일을 생성해준다.

각 파일들은 아래와 같은 기능을 처리한다.

- `views.py` : 홈페이지, 자기소개 페이지, 포스트 CRUD 페이지 관련 처리
- `auth.py` : 로그인, 로그아웃 등의 로그인 관련 기능 처리

이제 파일을 작성해보자.

<details open>
<summary>views.py</summary>
<div markdown="1">

{% highlight python linenos %}
from flask import Blueprint

views = Blueprint("views", __name__) # Blueprint 만들기

@app.route("/")
def home():
    return "This Is Home"

@app.route("/about_me/")
def about_me():
    return "Introduce About Me..."
{% endhighlight %}

</div>
</details>

작성 후 `__init__.py` 에 Blueprint 를 등록해주면 된다.

<details open>
<summary>__init__.py</summary>
<div markdown="1">

{% highlight python linenos %}
def create_app():
    app = Flask(__name__)
    app.config['SECRET_KEY'] = "IFP"

    from .views import views
    # blueprint 등록, '/blog' 를 기본으로 한다.
    app.register_blueprint(views, url_prefix="/blog")

    return app
{% endhighlight %}

</div>
</details>

이제 `/blog/` 와 `/blog/about_me` 의 주소로 접속이 되는 것을 확인할 수 있다.

### 그래서…

Blueprint는 url의 basename을 정해줄 수 있다.

```python
app.register_blueprint(views, url_prefix='/blog')
```

이와 같이 Flask는 Blueprint를 이용하여 url을 관리한다.

## render_template()

우리는 이미지도 있고, 여러가지 크기의 텍스트도 있으며 버튼을 누르면 다른 링크로 넘어가는 기능이 있는 블로그를 원한다.

하지만 여태 사용해왔던 html 태그로 된 문자열을 반환하는 함수로는 무리가 있을 것이다.

> 아예 html 파일을 반환하면 되지 않을까?

이때 `render_template()`을 사용한다.

`render_template`은 기본적으로 templates라는 폴더를 찾아 html 파일들을 불러온다.

사용하기에 앞서 `view.py`와 `auth.py`에 `render_template`을 `import` 해준다.

```python
from flask import Blueprint, render_template
```

그리고 blog 패키지 아래에 templates란 디렉토리를 생성해주고, 그 아래에 `home.html` 파일을 작성해준다.

{% highlight html linenos %}
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Test home</title>
</head>
<body>
    <h1>테스트입니다.</h1>
    <P>잘 보이네요...</P>
</body>
</html>
{% endhighlight %}

그리고 view에 home을 아래와 같이 수정해준다면…

{% highlight python linenos %}
@views.route("/")
def home():
    return render_template("index.html")
{% endhighlight %}

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/test1.png" alt="">
  <figcaption>/blog/ 로 접속시 작성한 html 파일 그대로 나오는 것을 확인할 수 있다.</figcaption>
</figure>


### 값 전달

render_template 함수가 구현되어 있는 곳을 가보면

{% highlight python linenos %}
def render_template(
    template_name_or_list: t.Union[str, Template, t.List[t.Union[str, Template]]],
    **context: t.Any
) -> str:
    """Renders a template from the template folder with the given
    context.

    :param template_name_or_list: the name of the template to be
                                  rendered, or an iterable with template names
                                  the first one existing will be rendered
    :param context: the variables that should be available in the
                    context of the template.
    """
    ctx = _app_ctx_stack.top
    ctx.app.update_template_context(context)
    return _render(
        ctx.app.jinja_env.get_or_select_template(template_name_or_list),
        context,
        ctx.app,
    )
{% endhighlight %}

위와 같이 나와있다.

2번째 행을 보면 첫번째 인자로 template_name_or_list를 받고, 다음 인자로 context를 인자로 받는다고 나와있다.

그럼 context로 값을 전달해보자.

`home()`을 아래와 같이 수정해준다.

{% highlight python linenos %}
@views.route("/")
def home():
    return render_template("home.html", name="홍길동", message="아버지를 아버지라 부르지 못하고...")
{% endhighlight %}

html 파일은 아래와 같이 수정해준다.

{% highlight html linenos %}
<body>
    <h1>테스트입니다.</h1>
    <P>잘 보이네요...</P>
    {% raw %}
    <p>이름 : {{ name }}</p>
    <p>메시지 : {{ message }}</p>
    {% endraw %}
</body>
{% endhighlight %}

<figure style="width: 500px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/blog/test2.png" alt="">
  <figcaption>역시나 잘 나오는 것을 확인할 수 있다.</figcaption>
</figure>

> &#123;&#123; ... &#125;&#125;?

위 html 파일에서 중괄호 2개를 볼 수 있다.

이는 무엇일까? 아래 jinja 템플릿 엔진에서 알아보도록 하자.

## jinja 템플릿 엔진

jinja는 파이썬용 웹 템플릿 엔진이다.

**웹 템플릿 엔진?** 템플릿 양식과 입력 자료를 결합하여 웹문서를 출력하는 소프트웨어를 말한다.
{: .notice}

jinja 는 중괄호 2개를 변수로 인식하고, 전달된 context에서 적절한 값을 찾아 렌더링하게 된다.

추가로 아래와 같은 문법들이 있다.


- &#123;% ... %&#125; : 프로그래밍 영역

- &#123;{ ... }&#125; : 변수 영역

- {# ... #} : 주석 영역

## 마치며

다음 포스트에서는 정적 파일을 이용해 블로그 디자인을 해보겠다.

**Notice:** 사용한 코드는 [이 곳](https://github.com/overtae/ifp-flask-study/tree/03-blog-practice)에서 확인가능합니다.
{: .notice--info}