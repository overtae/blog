---
title: "[파이썬 플라스크] Flask로 HTTP API 구축하기"
date: 2022-08-24
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "아이템 추가, 조회 기능 구현"
---

## HTTP란?

HTTP란 Hyper Text Transfer Protocol의 약어로 인터넷에서 데이터를 주고 받을 수 있는 포로토콜(규칙)을 말하며, 클라이언트-서버 모델을 따른다.

> 클라이언트-서버 모델?

클라이언트가 요청을 생성하기 위한 연결을 연 다음 응답을 받을 때까지 대기하는 모델을 말한다.

### 동작 방식

HTTP는 클라이언트가 서버에 요청을 보내면 서버는 그에 대한 응답을 보내준다.

> 클라이언트? 서버?

- 클라이언트 : 인터넷에 연결된 사용자의 디바이스 등 웹에 접근할 수 있는 소프트웨어
- 서버 : 클라이언트가 요청하면 해당 요청을 처리하는 역할

## HTTP Verbs (요청 메소드)

먼저 HTTP Verbs란 클라이언트가 웹 서버에게 요청의 목적을 알리는 수단이다.

많이 쓰이는 메소드로는 아래와 같다.

- GET : 리소스의 표시를 요청. 데이터의 수정 없이 데이터를 받기만 한다.
- HEAD : GET과 동일하지만 HTTP 헤더 정보만을 보낸다.
- POST : 특정 리소스를 제출한다.
- PUT : 목적 리소스의 모든 현재 표시를 요청하는 값으로 바꾼다.
- DELETE : 특정 리소스를 삭제한다.
- PATCH : 리소스의 부분만을 수정한다.

## REST 원칙

REST란 REpresentational State Transfer의 약어로 클라이언트와 서버가 데이터를 주고 받는 방식에 대한 아키텍처 스타일이다.

총 여섯 가지의 기본 원칙이 있으며 이 가이드를 준수한 인터페이스를 RESTful하다고 한다.

### 6가지 원칙

---

#### Uniform Interface 일관된 인터페이스

URL만 보고 어떤 정보가 들어올지 예측 가능하게 하나의 URL로는 한개의 데이터만 가져와야 한다.~~~~

#### Client-server 구조

클라이언트와 서버는 서로 독립적이어야 하며 클라이언트는 요청을, 서버는 응답을 해야한다.

#### Stateless 무상태성

요청들은 각각 독립적으로 처리되어야 한다.

하나의 요청이 처리되어야 다른 요청을 보내주는 방식은 안된다.

#### Cacheable 캐싱

요청을 통해 보내는 자료들은 캐싱이 가능해야 한다.

캐싱이란 브라우저가 자동으로 이미지나 css 파일 등을 저장하는 것을 말한다.

#### Layered system 다중 계층

여러개의 레이어를 거쳐 요청을 처리해도 된다.

#### Code on demand

서버는 실제 실행 가능한 코드를 전송해줄 수도 있다.

## HTTP API 구축

shop을 추가하고, 각각의 shop들의 아이템을 조회하는 API를 만들어보자.

{% highlight python linenos %}
from flask import Flask, jsonify
from shop_list import shop_list

app = Flask(__name__)

# 새로운 shop 생성
@app.route('/shop', methods=['POST'])
def create_shop():
    pass

# shop_name을 받아 해당하는 shop을 보여줌
@app.route('/shop/<string:shop_name>', methods=['GET'])
def get_shop_detail():
    pass

# shop들의 목록을 보여줌
@app.route('/shop', methods=['GET'])
def get_shop_list():
    pass

# shop_name을 받아 해당하는 shop에 아이템 추가
@app.route('/shop/<string:shop_name>/item', methods=['POST'])
def create_item_in_shop():
    pass

# shop_name을 받아 해당하는 shop의 아이템을 보여줌
@app.route('/shop/<string:shop_name>/item', methods=['GET'])
def get_item_list_in_shop(shop_name):
    pass

if __name__ == '__main__':
    app.run()
{% endhighlight %}


## 목록 조회

우선, 간단한 리스트를 만들어보자.

<details>
<summary>shop_list.py</summary>
<div markdown="1">

{% highlight python linenos %}
shop_list = [{
  'name' : 'Smith flower store',
  'items' : [{
    'name' : '장미',
    'price' : '1800',
  }]
}]
{% endhighlight %}

</div>
</details>

위와 같이 딕셔너리로 된 자료구조를 JSON 형식으로 바꿔주려면 플라스크의 `jsonify` 메소드를 사용하면 된다.

`get_shop_list()`를 아래와 같이 수정하자.

<details>
<summary>app.py</summary>
<div markdown="1">

{% highlight python linenos %}
from flask import Flask, jsonify
from shop_list import shop_list
...
@app.route('/shop', methods=['GET'])
def get_shop_list():
    return jsonify(shop_list)
{% endhighlight %}

</div>
</details>

`/shop`으로 접속해본다면 아래와 같이 나올 것이다.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/api/01-01.png" alt="">
  <figcaption>문자가 깨지고 들여쓰기가 안되어 있다.</figcaption>
</figure>

### 데이터 예쁘게 보이기

---

{% highlight python linenos %}
app.config['JSON_AS_ASCII'] = False
app.config['JSONIFY_PRETTYPRINT_REGULAR'] = True
{% endhighlight %}

위 두 줄의 코드만 추가해준다면,

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/api/01-02.png" alt="">
  <figcaption>보기 쉽게 나온다.</figcaption>
</figure>

## key 추가

우리는 여러 shop을 받을 것이므로 데이터를 다루기 쉽게 `shop_list`라는 key를 추가해주자.

{% highlight python linenos %}
@app.route('/shop', methods=['GET'])
def get_shop_list():
    return jsonify({'shop_list':shop_list})
{% endhighlight %}

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/api/01-03.png" alt="">
  <figcaption>shop_list가 추가된 것을 볼 수 있다.</figcaption>
</figure>

## shop 생성

shop의 name을 받아 새로운 shop을 생성하는 코드를 작성해주자.

{% highlight python linenos %}
@app.route('/shop', methods=['POST'])
def create_shop():
    request_data = request.get_json()
    new_shop = {
        'name': request_data['name'],
        'items':[]
    }
    shop_list.append(new_shop)
    return jsonify(new_shop)
{% endhighlight %}

### 테스트

---

편한 테스트를 위해 [포스트맨](https://www.postman.com/)을 사용해주었다.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/api/01-05.png" alt="">
  <figcaption>새로운 shop이 잘 추가되었다.</figcaption>
</figure>

## shop 상세 조회

`shop_name`을 받아 해당하는 shop의 정보를 보여주게끔 해보자.

{% highlight python linenos %}
@app.route('/shop/<string:shop_name>', methods=['GET'])
def get_shop_detail(shop_name):
    for shop in shop_list:
        if shop['name'] == shop_name:
            return jsonify(shop)
    return jsonify({'message' : 'shop not found'})
{% endhighlight %}

테스트를 위해 임의의 데이터도 추가해주었다.

<details>
<summary>shop_list.py</summary>
<div markdown="1">

{% highlight python linenos %}
shop_list = [{
    'name' : 'Smith flower store',
    'items' : [{
      'name' : '장미',
      'price' : '1800',
    }]
  }, 
  {
    'name' : '농심',
    'items' : [{
      'name' : '너구리',
      'price' : '950',
    }]
  }
]
{% endhighlight %}

</div>
</details>

그리고 요청을 보낸다면,

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/api/01-06.png" alt="">
  <figcaption>농심에 해당하는 결과만 나오는 것을 확인할 수 있다.</figcaption>
</figure>

## item 목록 조회

`shop_name`을 받아 해당하는 shop의 아이템들을 보여주도록 하자.

{% highlight python linenos %}
@app.route('/shop/<string:shop_name>/item', methods=['GET'])
def get_item_list_in_shop(shop_name):
    for shop in shop_list:
        if shop['name'] == shop_name:
            return jsonify({'items' : shop['items']})
{% endhighlight %}

위의 코드를 작성해주고 요청을 보내면,

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/api/01-07.png" alt="">
  <figcaption>items만 나온다.</figcaption>
</figure>

## item 생성

아이템의 이름과 가격을 받아 해당하는 shop에 추가하는 기능을 구현해보자.

{% highlight python linenos %}
@app.route('/shop/<string:shop_name>/item', methods=['POST'])
def create_item_in_shop(shop_name):
    request_data = request.get_json()
    for shop in shop_list:
        if shop['name'] == shop_name:
            new_item = {
                'name' : request_data['name'],
                'price' : request_data['price']
            }
            shop['items'].append(new_item)
            return jsonify(new_item)
    return jsonify({'message' : 'shop not found'})
{% endhighlight %}

작성 후 테스트 해본다면,

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/api/01-08.png" alt="">
  <figcaption>잘 추가가 되었다.</figcaption>
</figure>