---
permalink: /python/python-05/
title: "[파이썬 코딩 도장] Unit 15 문제 풀이"
date: 2022-01-12
categories:
  - Python dojang
tags:
  - python
  - 코딩 도장
excerpt: elif 사용하기
---

<div class="notice--success" markdown="1">
**15.3 연습 문제 : **

다음 소스 코드를 완성하여 변수 x가 11과 20 사이면 '11~20',<br>
21과 30 사이면 '21~30', 아무것도 해당하지 않으면<br>
'아무것도 해당하지 않음'이 출력되게 만드세요.

```python
x = int(input())

____________________
____________________
____________________
____________________
____________________
____________________
# 입력 : 5
# 결과 : 아무것도 해당하지 않음

```
</div>

<div class="notice" markdown="1">
**풀이**

```python
if 11 <= x <= 20:
  print('11~20')
elif 21 <= x <= 30:
  print('21~30')
else:
  print('아무것도 해당하지 않음')
```
조건식을 만들 때 `x >= 11 and x <= 20`처럼 and를 사용하는 방법도 있다.
</div>

<div class="notice--success" markdown="1">
**15.4 심사 문제 : **

표준 입력으로 나이(만 나이)가 입력됩니다.<br>
(입력 값은 7 이상 입력됨)<br>
교통카드 시스템에서 시내버스 요금은 다음과 같으며<br>
각 나이에 맞게 요금을 차감한 뒤 잔액이 출력되게 만드세요.<br>
(if, elif 사용)<br>
현재 교통카드에는 9,000원이 들어있습니다.
- 어린이(초등학생, 만 7세 이상 12세 이하): 650원
- 청소년(중∙고등학생, 만 13세 이상 18세 이하): 1,050원
- 어른(일반, 만 19세 이상): 1,250원

```python
age = int(input())
balance = 9000    # 교통카드 잔액
____________________
____________________
____________________
____________________
____________________
____________________
print(balance)
# 입력 : 17
# 결과 : 7950
```
</div>

<div class="notice" markdown="1">
**풀이**

```python
if age >= 19:
  balance -= 1250
elif age >= 13 and age <= 18:
  balance -= 1050
else:
  balance -= 650
```
</div>

**Notice:** 이 게시물은 파이썬 코딩 도장 사이트를 참고하였습니다.
[https://dojang.io/course/view.php?id=7](https://dojang.io/course/view.php?id=7)
{: .notice--info}