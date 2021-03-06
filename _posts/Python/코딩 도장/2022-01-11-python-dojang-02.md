---
permalink: /python/python-03/
title: "[파이썬 코딩 도장] Unit 13 문제 풀이"
date: 2022-01-11
categories:
  - Python dojang
tags:
  - python
  - 코딩 도장
excerpt: if 조건문 사용하기
---

<div class="notice--success" markdown="1">
**13.6 연습 문제 :**

다음 소스 코드를 완성하여 x의 값이 10이 아닐 때 'ok'가 출력되게 만드세요.

```python
x = 5

if _________________:
    print('ok')
```

</div>

<div class="notice" markdown="1">
**풀이**

`x != 10`<br>
만약 객체를 비교할 경우 `is`, `is not` 연산자를 사용해도 된다.

</div>

<div class="notice--danger" markdown="1">
**13.7 심사 문제 :**

표준 입력으로 가격(정수)과 쿠폰 이름이 각 줄에 입력됩니다.<br>
Cash3000 쿠폰은 3,000원, Cash5000 쿠폰은 5,000원을 할인합니다.<br>
쿠폰에 따라 할인된 가격을 출력하는 프로그램을 만드세요.<br>
(input에서 안내 문자열은 출력하지 않아야 합니다)

```python
______________
______________
______________
______________
______________
______________
______________

# 입력 :
# 27000
# Cash3000
# 결과 : 24000
```

</div>

<div class="notice" markdown="1">
**풀이**

```python
a = int(input())
b = input()
if b == 'Cash3000':
    print(a - 3000)
if b == 'Cash5000':
    print(a - 5000)
```

처음에 b 입력을 `string(input())`이라 썼더니 에러가 났다. input()으로 받은 값은 원래 string형이므로 바꿀 필요도 없고 문자열로 변환하고 싶다면 `str()`을 써줘야 한다..<br>
if문에서는 `a -= 3000` 이런식으로 계산만 하고 나중에 값을 출력하는 방법도 있다.

</div>

**Notice:** 이 게시물은 파이썬 코딩 도장 사이트를 참고하였습니다.
[https://dojang.io/course/view.php?id=7](https://dojang.io/course/view.php?id=7)
{: .notice--info}
