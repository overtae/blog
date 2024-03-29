---
title: "[파이썬 플라스크] Werkzeug의 PathConverter, BaseConverter"
date: 2022-11-12
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "Flask Instagram Clone Coding"
---

## URL 내부의 매개변수

Flask에서는 `<>`을 이용해 url로 변수를 받을 수 있다. 원하는 위치에 `<반환 타입:변수명>` 나 `<변수명>` 을 적어주면 되는데, 타입을 명시하지 않은 경우 기본 string형으로 받아온다. 사용할 수 있는 타입으로는 아래와 같다. 이 중에서 **path 타입**에 대해 좀 더 알아보려고 한다.

| 타입<br/>Converter          | 설명                                 | 정규표현식 표현                                                                           |
| --------------------------- | ------------------------------------ | ----------------------------------------------------------------------------------------- |
| string<br/>UnicodeConverter | 기본값. 슬래시(/)를 제외한 모든 문자 | [^/]{minlength, maxlength}                                                                |
| any<br/>AnyConverter        | 여러 값의 조합                       | '(?:%s)' % '\|'.join([re.escape(x) for x in items])                                       |
| path<br/>PathConverter      | string과 비슷하지만 슬래시를 포함    | [^/].*?                                                                                   |
| int<br/>IntegerConverter    | 양의 정수                            | r'\d+'                                                                                    |
| float<br/>FloatConverter    | 양의 부동 소수점 값                  | r'\d+\.\d+'                                                                               |
| uuid<br/>UUIDConverter      | 범용 고유 식별자                     | r'[A-Fa-f0-9]{8}-[A-Fa-f0-9]{4}-' \ <br/>r'[A-Fa-f0-9]{4}-[A-Fa-f0-9]{4}-[A-Fa-f0-9]{12}' |

## PathConverter의 정규표현식

`werkzeug.routing`의 `PathConverter` 코드는 아래와 같다. — [참고](https://tedboy.github.io/flask/_modules/werkzeug/routing.html)

{% highlight python linenos %}
class PathConverter(BaseConverter):

    """Like the default :class:`UnicodeConverter`, but it also matches
    slashes.  This is useful for wikis and similar applications::

        Rule('/<path:wikipage>')
        Rule('/<path:wikipage>/edit')

    :param map: the :class:`Map`.
    """
    regex = '[^/].*?'
    weight = 200
{% endhighlight %}

여기서 우리는 11행의 regex만 보면 된다. 의미는 아래와 같다.

`[^/]` : 슬래시를 포함하지 않는 문자 중,

`.` : 줄바꿈 문자를 제외한 모든 문자를

`*` : 0개 이상 찾아라

`?` : 가능한 최소로


<details open>
<summary>가능한 최소, non-greedy</summary>
<div markdown="1">
    
결과로 구분하면 쉽게 알 수 있다.

/hello/flask/ 문자열이 주어졌을 때,

- `[^/].*`
    
  /<span style="background-color:#3b9cba;">hello/flask/</span>
    
- `[^/].*?`
    
  /<span style="background-color:#3b9cba;">hello</span>/<span style="background-color:#3b9cba;">flask</span>/

</div>
</details>


## BaseConverter의 정규표현식

BaseConverter의 정규식은 `[^/]{minlength(>= 1), maxlength}` 이다. 최대 길이가 주어지지 않은 경우 `[^/]{1,}` 가 된다. 의미는 아래와 같다.

`[^/]` : 슬래시를 포함하지 않는 문자 중,

`{minlength, maxlength}` : minlength개 이상 maxlength개 이하로 찾아라

## BaseConverter vs. PathConverter

BaseConverter와 PathConverter의 가장 큰 차이는 **슬래시 포함 여부**이다.

정규표현식의 차이로는 아래 이미지를 보면 쉬울 것이다. 

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/instagram/regex.png" alt="">
</figure>

둘 다 정규표현식은 슬래시를 포함하지 않지만, 아마도 Converter나 다른 메소드가 추가적인 작업을 해주는 것 같다. (고 생각한다.)
