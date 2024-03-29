---
title: "[파이썬 플라스크] 플러거블 뷰 알아보기"
date: 2022-08-30
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "클래스 기반의 코드 작성"
---

## 플러거블 뷰 Pluggable Views ?

함수 대신 클래스를 기반으로 하는 Django의 제네릭 뷰(Generic View)에 영감을 받아 Flask 0.7에서 도입한 것이 플러거블 뷰이다.

블러거블 뷰는 말 그대로 '끼워넣는 뷰'로 사용자가 정의 가능한 뷰를 가질 수 있다.

### class View

플러거블 뷰를 사용하기 위해서 먼저 `View`를 상속 받은 클래스를 만들고 `dispatch_request()`를 구현해야한다.

그 다음, `as_view()` 메소드를 사용해 엔드포인트 이름을 전달해주어야한다.

이 과정을 마쳤다면, `dispatch_request()` 메소드를 view function으로 다룰 수 있다.

아래 코드에서 각각이 무슨 역할을 하는지 알아보자.

{% highlight python linenos %}

from flask.views import View

# flask.views의 View 클래스 상속
class TestItems(View):
  # 허용할 메소드 지정
  methods = ['GET', 'POST']

  def dispatch_requests(self):
    items = Item.query.all()
    retrun render_template('items.html', objects=items)

# app.add_url_rule : URL 등록
# as_view() : URL의 엔드포인트 전달
app.add_url_rule('/items/', view_func=TestItems.as_view('test_items'))

{% endhighlight %}

### class MethodView(View)

MethodView 클래스는 View 클래스를 상속 받은 클래스로 기본적으로 `get(), post(), put(), delete()` 메소드가 존재한다.

해당 메소드들을 구현하면 GET, POST, PUT, DELETE 요청을 처리할 수 있는 것이다.

**Notice:** 이 게시물은 [Pluggable Views 공식 문서](https://flask-docs-kr.readthedocs.io/ko/latest/views.html#api) 사이트를 참고하였습니다.
{: .notice--info}