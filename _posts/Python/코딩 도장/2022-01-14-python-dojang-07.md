---
permalink: /python/python-08/
title: "[파이썬 코딩 도장] Unit 18 문제 풀이"
date: 2022-01-14
categories:
  - Python dojang
tags:
  - python
  - 코딩 도장
excerpt: 반복문에 break, continue 사용하기
---

<div class="notice--danger" markdown="1">
**18.5 연습 문제 :**

다음 소스 코드를 완성하여 0과 73 사이의 숫자 중 3으로 끝나는 숫자만 출력되게 만드세요.
```python
i = 0
while True:
  ____________
    __________
    __________
  ____________
    __________
    print(i, end=' ')
    i += 1
```
</div>

<div class="notice" markdown="1">
**풀이**

```python
if i % 10 != 3:
  i += 1
  continue
if i > 73:
  break
```
첫번째 if 조건문에 `i += 1`을 안적고 `break`를 적었었는데 이렇게 하면 while문을 아예 빠져나가게 되므로 안된다.<br>
또 continue를 쓸 경우 아래쪽에 출력문과 `i += 1`도 건너뛰어 버리므로 continue하기 전에 1을 더해주어야 한다.
</div>

<div class="notice--danger" markdown="1">
**18.6 심사 문제 :**

표준 입력으로 정수 두 개가 입력됩니다.<br>
(첫 번째 입력 값의 범위는 1~200, 두 번째 입력 값의 범위는 10~200이며 첫 번째 입력 값은 두 번째 입력 값보다 항상 작습니다)<br>
다음 소스 코드를 완성하여 첫 번째 정수와 두 번째 정수 사이의 숫자 중 3으로 끝나지 않는 숫자가 출력되게 만드세요.<br>
정답에 코드를 작성할 때는 while True:에 맞춰서 들여쓰기를 해주세요.

```python
start, stop = map(int, input().split())
 
i = start
 
while True:
_____________________
_____________________
_____________________
_____________________
_____________________
    print(i, end=' ')
    i += 1
# 입력 : 1 20
# 결과 : 1 2 4 5 6 7 8 9 10 11 12 14 15 16 17 18 19 20
```
</div>

<div class="notice" markdown="1">
**풀이**

```python
  if i % 10 == 3:
    i += 1
    continue
  if i > stop:
    break
```
i를 start로 써서 틀렸다. 문제를 잘 읽도록 하자.
</div>

**Notice:** 이 게시물은 파이썬 코딩 도장 사이트를 참고하였습니다.
[https://dojang.io/course/view.php?id=7](https://dojang.io/course/view.php?id=7)
{: .notice--info}