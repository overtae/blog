---
permalink: /python/python-10/
title: "[파이썬 코딩 도장] Unit 20 문제 풀이"
date: 2022-01-15
categories:
  - Python dojang
tags:
  - python
  - 코딩 도장
excerpt: FizzBuzz 문제
---

<div class="notice--success" markdown="1">
**20.7 연습 문제 :**

다음 소스 코드를 완성하여 1부터 100까지의 숫자를 출력하면서 2의 배수일 때는 'Fizz',<br>
11의 배수일 때는 'Buzz', 2과 11의 공배수일 때는 'FizzBuzz'가 출력되게 만드세요.

```python
for i in range(1, 101):
    if 1._________________________:
        print('FizzBuzz')
    elif 2.___________:
        print('Fizz')
    elif 3.___________:
        print('Buzz')
    else:
        print(i)
```
</div>

<div class="notice" markdown="1">
**풀이**

```python
# 1
i % 2 == 0 and i % 11 == 0
# 2
i % 2 == 0
# 3
i % 11 == 0
```
공배수 같은 경우 논리 연산자 사용 대신 `i % 22 == 0` 이런식으로 최소 공배수를 직접 구해 쓸 수 있다.<br>
하지만 실무에선 논리 연산자를 사용한 조건처럼 의미를 명확하게 밝히는게 좋다고 한다.<br>
그 편이 가독성이 좋아져 읽기 쉽고 이해하기 쉽기 때문이다.
</div>

<div class="notice--success" markdown="1">
**20.8 심사 문제 :**

표준 입력으로 정수 두 개가 입력됩니다.<br>
(첫 번째 입력 값의 범위는 1~1000, 두 번째 입력 값의 범위는 10~1000이며 첫 번째 입력 값은 두 번째 입력 값보다 항상 작습니다)<br>
첫 번째 정수부터 두 번째 정수까지 숫자를 출력하면서 5의 배수일 때는 'Fizz',<br>
7의 배수일 때는 'Buzz', 5와 7의 공배수일 때는 'FizzBuzz'를 출력하는 프로그램을 만드세요.<br>(input에서 안내 문자열은 출력하지 않아야 합니다)

```python
# 입력 : 35 40
# 결과 : 
# FizzBuzz
# 36
# 37
# 38
# 39
# Fizz
```
</div>

<div class="notice" markdown="1">
**풀이**

```python
x, y = map(int, input().split())
for i in range(x, y + 1):
  if i % 5 == 0 and i % 7 == 0:
    print('FizzBuzz')
  elif i % 5 == 0:
    print('Fizz')
  elif i % 7 == 0:
    print('Buzz')
  else:
    print(i)

# 단축
for i in range(x, y + 1):
    print('Fizz' * (i % 5 == 0) + 'Buzz' * (i % 7 == 0) or i)
```

</div>

**Notice:** 이 게시물은 파이썬 코딩 도장 사이트를 참고하였습니다.
[https://dojang.io/course/view.php?id=7](https://dojang.io/course/view.php?id=7)
{: .notice--info}