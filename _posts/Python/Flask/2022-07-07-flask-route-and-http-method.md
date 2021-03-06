---
title: "[파이썬 플라스크] 라우팅과 http 메소드"
date: 2022-07-07
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "플라스크 공식 문서 정리"
---

## 라우팅 Routing

플라스크에서 함수는 주소를 입력하여 들어온 요청을 담당하는 담당자 역할을 한다.

라우팅이란 어떤 요청에 어떤 함수가 응답할 것인가를 연결시켜주는 작업을 말한다.

플라스크 라우팅에는 `route()` 데코레이터가 사용된다.

<details>
<summary>데코레이터(장식자)란?</summary>
<div markdown="1">

{% highlight python linenos %}
@데코레이터
def 함수이름():
    코드
{% endhighlight %}

데코레이터는 위와 같은 문법으로 사용되며, 기존 함수를 수정하지 않으면서 추가 기능을 구현할 때 사용한다.

</div>
</details>

{% highlight python linenos %}

# route() 데코레이터는 url와 함수를 연결해준다.

# route라는 데코레이터에 슬래시(/)
# 사용자가 아무런 path를 입력하지 않았을 때 아래 함수(담당자)가 응답
@app.route('/')
def index():
	return 'Welcome!! This is Index Page' # 'Welcome!! This is Index Page'라는 정보를 응답

# 사용자가 path에 '/hello'를 입력 했을 때
@app.route('/hello')
def hello():
	return 'Hello, Flask' # 'Hello, Flask'라는 정보를 응답

{% endhighlight %}

## 변수의 규칙 Variable Rules

꺽새(`<>`)를 이용해 path에 변수를 받을 수 있다.

이때, 변수에 입력된 값이 같은 이름의 파라미터에 전달되므로 변수명과 함수의 파라미터명이 동일해야 한다.

{% highlight python linenos %}

@app.route('/user/<username>')
def show_user_profile(username):
	# show the user profile for that user
	return f'User {escape(username)}'

@app.route('/read/<id>/')
def read(id):
	print(id) # 터미널에 출력
	return 'Read ' + id

{% endhighlight %}

이때, 변수의 자료형은 기본적으로 **문자열(String)**형이다.

만약 다른 자료형으로 사용하고 싶을 경우, 꺽새 안에 자료형을 명시해주면 flask가 자동으로 해당 자료형으로 변환해준다.

{% highlight python linenos %}

@app.route('/read/<int:id>/')
def read(id):
	print(type(id)) # 정수형(int)

{% endhighlight %}

## 후행슬래시 Trailing Slash

뒤에 `/` 가 붙으면 폴더(디렉토리) 안붙으면 파일이다.

하지만 HTTP 스펙상으로는 Request에는 경로를 생략 하면 안된다.

따라서 `/` 를 생략한 경우 서버에는 뒤에 `/` 를 붙여서 url를 보내게 된다.

정리를 하자면,

- `/about` -> `/about` 접근만 허용, `/about/` 요청 시 404 오류

- `/about/` -> `/about` 요청 시 `/about/` 로 자동 리다이렉션

**리다이렉션(Redirection):** 지정위치로 이동하는 것을 말한다.
{: .notice}

공식 문서에는 아래와 같이 나와있다.

{% highlight python linenos %}

@app.route('/projects/')
def projects():
    return 'The project page'

@app.route('/about')
def about():
    return 'The about page'

{% endhighlight %}

`projects` 엔드포인트의 표준 URL에는 후행 슬래시가 있다. 이것은 파일 시스템의 폴더와 유사하다. 만약 유저가 후행 슬래시 없이 URL에 접근한다면(`/projects`) 플라스크가 유저를 후행 슬래시와 함께 표준 URL로 리다이렉트 한다(`/projects/`).

`about` 엔드포인트의 표준 URL은 후행 슬래시를 가지고 있지 않다. 이것은 파일의 pathname과 비슷하다. 후행 슬래시와 함께 접근하는것(`/about/`)은 404 *"Not Found"* 에러를 발생시킨다. 이렇게 하면 이러한 리소스들이 고유한 URL을 유지하여 검색 엔진이 동일한 페이지를 두 번 인덱싱하는 것을 방지할 수 있다.

404 에러를 방지하기 위해 플라스크에는 URL리버싱 함수인 `url_for()` 함수가 있다.

## URL 구축 URL Building

`url_for()` 함수는 함수 이름으로 된 url을 생성해준다.

함수의 첫번째 인자로 함수의 이름을 받고 각각 URL 규칙의 변수 부분에 맞는 키워드 인자를 전달 받는다.

만약, 파일 내에 index, about_page, create라는 함수가 있을 경우 아래와 같이 사용한다면 해당 함수의 url을 생성해준다.

{% highlight python linenos %}

@app.route('/')
def index():
    return 'This is Home page.' \
           f'<p><a href="{url_for("index")}">Main</a></p>' \
           f'<p><a href="{url_for("about_page")}">About this page</a></p>' \
           f'<p><a href="{url_for("create")}">Create user</a></p>'

{% endhighlight %}

아래 예제는 함수 이름과 키워드를 전달 받은 모습이다.

{% highlight python linenos %}

url_for('login', next='/')) # /login?next=/
url_for('profile', username='John Doe')) # /user/John%20Doe

{% endhighlight %}

## http 메소드 HTTP Methods

대표적인 정보 전달 방식으로는 아래 두 가지가 있다.

### GET 방식 : url을 통한 데이터 전송

동적으로 동작하는 웹서비스에서 특정한 페이지를 식별하는 고유한 주소로서 사용된다.

보통 웹페이지의 데이터를 사용자가 가져올 때 사용한다.

### POST 방식 : url에 정보 포함 x

웹페이지의 데이터를 사용자가 변경하도록 할 때 사용된다.

큰 데이터들을 안전하게 전송이 가능하다.

### 돌아와서...

html 태그 중 form 태그의 method 속성 기본 값은 GET이다.

즉, 생략할 경우 GET 방식이라는 뜻이다.

이걸 POST로 바꿔주게 된다면 url의 변화 없이 값을 전해줄 수 있게 된다.

{% highlight python linenos %}

@app.route('/create/')
def create():
	content = '''
	    <form action="/create/" method="POST">
          <p><input type="text" name="name" placeholder="name"></p>
          <p><input type="number" name="age" placeholder="age"></p>
          <p><textarea name="msg" placeholder="msg"></textarea></p>
          <p><input type="submit" value="create"></p>
	    </form>
	'''

{% endhighlight %}

위의 라우트는 GET 방식만 받는 라우트이다.

따라서 위의 방식으로 POST를 사용할 경우 허용되지 않았다는 에러가 뜰 것이다.

이때, 라우트에 methods 속성을 추가해주면 에러가 사라지게 된다.

```python
@app.route('/create/', methods=['GET', 'POST'])
```

이럴 경우 사용자가 링크를 클릭하거나 입력해서 들어간 경우는 GET요청, 값을 전송한 경우는 POST 요청의 url이 되어야 한다.

이를 식별하기 위해 request라는 모듈 import 후 request.method를 사용한다.

{% highlight python linenos %}

@app.route('/create/', methods=['GET', 'POST'])
def create():
    if request.method == 'GET':
        content = '''
            <form action="/create/" method="POST">
                <p><input type="text" name="name" placeholder="name"></p>
                <p><input type="number" name="age" placeholder="age"></p>
                <p><textarea name="msg" placeholder="msg"></textarea></p>
                <p><input type="submit" value="create"></p>
            </form>
        '''
        return template(get_users(), content)
    elif request.method == 'POST':
        global next_id
        name = request.form['name']
        age = request.form['age']
        msg = request.form['msg']
        newuser = {'id': next_id, 'name': name, 'age': age, 'msg': msg}
        users.append(newuser)
        url = '/read/' + str(next_id) + '/'
        next_id = next_id + 1
        return redirect(url)

{% endhighlight %}

**Notice:** 이 게시물은 [flask.palletsprojects.com](https://flask.palletsprojects.com/en/2.1.x/quickstart/) 사이트를 참고하였습니다.
{: .notice--info}