---
permalink: /python/python-07/
title: "[파이썬 코딩 도장] Unit 17 문제 풀이"
date: 2022-01-13
categories:
  - Python dojang
tags:
  - python
  - 코딩 도장
excerpt: while 반복문 사용하기
---

<div class="notice--success" markdown="1">
**17.5 연습 문제 :**

다음 소스 코드를 완성하여 정수 2 5, 4 4, 8 3, 16 2, 32 1이 각 줄에 출력되게 만드세요.<br>
while에 조건식은 두 개 지정하고, 두 변수를 모두 변화시켜야 합니다.

```python
i = 2
j = 5

__________or____________
    print(i, j)
    __________
    __________

# 결과 : 
# 2 5
# 4 4
# 8 3
# 16 2
# 32 1
```
</div>

<div class="notice" markdown="1">
**풀이**

```python
while i < 33 or j > 0:
  print(i, j)
  i *= 2
  j -= 1
```
</div>

<div class="notice--danger" markdown="1">
**17.6 심사 문제 :**

표준 입력으로 금액(정수)이 입력됩니다.<br>
1회당 요금은 1,350원이고, 교통카드를 사용했을 때마다의 잔액을 각 줄에 출력하는 프로그램을 만드세요.<br>
(input에서 안내 문자열은 출력하지 않아야 합니다)<br>
단, 최초 금액은 출력하지 않아야 합니다.<br>
그리고 잔액은 음수가 될 수 없으며 잔액이 부족하면 출력을 끝냅니다.

```python
______________
______________
______________
______________
# 입력 : 10000
# 결과 : 
# 8650
# 7300
# 5950
# 4600
# 3250
# 1900
# 550
```
</div>

<div class="notice" markdown="1">
**풀이**

```python
x = int(input())
while x >= 1350:
  x -= 1350
  print(x)
```
조건을 `x > 0`으로 줬었는데, 이렇게 했을 경우 x의 최종값이 음수가 되므로 1,350원 보다 같거나 클 때로 조건을 줘야한다.
</div>

**Notice:** 이 게시물은 파이썬 코딩 도장 사이트를 참고하였습니다.
[https://dojang.io/course/view.php?id=7](https://dojang.io/course/view.php?id=7)
{: .notice--info}