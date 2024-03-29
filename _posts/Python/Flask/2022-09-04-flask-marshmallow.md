---
title: "[파이썬 플라스크] Marshmallow로 Python에서 직렬화/역직렬화 처리하기"
date: 2022-09-04
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "데이터 변환"
---

## 직렬화? 역직렬화?

직렬화와 역직렬화란 무엇일까?

> 직렬화 Serialization

메모리를 디스크에 저장하거나 객체를 네트워크 통신이 가능한 (연속된) 형태로 변환하는 것

> 역직렬화 Deserialization

디스크에 저장된 데이터를 읽거나 네트워크 통신으로 받은 데이터를 원래 객체의 형태로 복원하는 것

객체를 Byte나 CSV, XML, JSON 등으로 변환할 수도 있지만 구조적인 데이터의 경우 JSON 형태로의 변환을 권장한다고 한다.

## Python에서의 직렬화/역직렬화

그렇다면 파이썬에서의 직렬화는 어떻게 하는 걸까.

[marshmallow](https://marshmallow.readthedocs.io/en/stable/)는 JSON과 같은 데이터를 파이썬의 객체로 변환해주는 라이브러리이다.

공식 문서를 살펴보면 marshmallow schema는 아래와 같이 쓰일 수 있다 한다.

- 입력 데이터의 **유효성 검사**
- 입력 데이터를 앱 수준 개체로 **역직렬화**
- 앱 수준 개체를 원시 Python 유형으로 **직렬화** — HTTP API에서 사용하기 위해 JSON과 같은 표준 포맷으로 렌더링 가능

## marshmallow Schema 클래스

marshmallow Schema 클래스의 파라미터 중 몇 개만 알아봐보자. — [참고](https://marshmallow.readthedocs.io/en/stable/api_reference.html#marshmallow.Schema)

### dump_only: types.StrSequenceOrSet = ()

: 역직렬화 중에 건너뛸 필드(읽기 전용 필드)

### load_only: types.StrSequenceOrSet = ()

: 직렬화 중에 건너뛸 필드(쓰기 전용 필드)

### many: bool = False

: 객체가 collection일 경우 True로 설정해야 list로 직렬화 된다.

## 사용 예시

그럼 이제 marshmallow를 사용해보자.

가상환경을 활성화시킨 후 아래를 입력하여 marshmallow를 설치해준다.

```bash
$ pip install -U marshmallow
```

이름, 키, 몸무게, 성별, 나이, 주소, 직업 총 7가지의 정보를 갖는 사람 클래스 하나를 정의해주었다.

{% highlight python linenos %}
class Person:
    def __init__(self, name, height, weight, gender, age, address, job, ):
        self.name = name        # 이름
        self.height = height    # 키
        self.weight = weight    # 몸무게
        self.gender = gender    # 성별
        self.age = age          # 나이
        self.address = address  # 주소
        self.job = job          # 직업
{% endhighlight %}

그리고 임의의 사람 두 명을 구현하였다.

{% highlight python linenos %}
gildong = Person(
    '홍길동',
    '180cm',
    '80kg',
    'male',
    '24',
    '조선 땅 어느 곳이던 내가 가는 곳이 곧 나의 거처일 것이니...',
    '의적'
)

chulsu = Person(
    '김철수',
    '110cm',
    '28kg',
    'male',
    '7',
    '떡잎마을',
    '유치원생'
)
{% endhighlight %}

### 직렬화

---

{% highlight python linenos %}
from dataclasses import field
from marshmallow import Schema, fields


class PersonSchema(Schema):
    name = fields.String()
    height = fields.String()
    gender = fields.String()
    age = fields.String()


person_data_converter = PersonSchema()

gildong_data = person_data_converter.dump(gildong)
chulsu_data = person_data_converter.dump(chulsu)

print(gildong_data)
print(chulsu_data)
{% endhighlight %}

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/api/02-01.png" alt="">
  <figcaption>딕셔너리</figcaption>
</figure>

{% highlight python linenos %}
import json


print(json.dumps(gildong_data, ensure_ascii=False))
print(json.dumps(chulsu_data, ensure_ascii=False))
{% endhighlight %}

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/api/02-02.png" alt="">
  <figcaption>json</figcaption>
</figure>

### 역직렬화

---

{% highlight python linenos %}
from marshmallow import Schema, fields
from serialization import Person
import json

JSON_minji_data = {"name": "김민지",
                   "height": "160cm",
                   "weight": "45kg",
                   "gender": "female",
                   "age": 25,
                   "address": "그대 마음 속..",
                   "job": "회사원"}


class PersonSchema(Schema):
    name = fields.String()
    height = fields.String()
    weight = fields.String()
    gender = fields.String()
    age = fields.Integer()
    address = fields.String()
    job = fields.String()


person_data_converter = PersonSchema()

data = person_data_converter.load(JSON_minji_data)
Person_object = Person(**data)

print(Person_object.__dict__)
{% endhighlight %}

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/api/02-03.png" alt="">
  <figcaption>Person_object</figcaption>
</figure>