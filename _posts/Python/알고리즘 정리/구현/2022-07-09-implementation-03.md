---
title: "[파이썬 알고리즘] 왕실의 나이트 문제 풀이"
date: 2022-07-09
categories:
  - Python Algorithm
tags:
  - python
  - 알고리즘
  - 구현
excerpt: "구현 알고리즘을 이용하여 문제를 풀어보자"
---

### ❓ 문제

---

![문제-1]({{ site.url }}{{ site.baseurl }}/assets/images/python/implementation/02-01.png){: .align-center}

![문제-2]({{ site.url }}{{ site.baseurl }}/assets/images/python/implementation/02-02.png){: .align-center}


### 💡 문제 해결 아이디어

---

이 문제 역시 전 문제와 마찬가지로 **요구사항대로 충실히 구현**하면 된다.

나이트는 2가지 경로로 움직일 수 있는 규칙이 있다.

1. 수평으로 두 칸 이동한 뒤에 수직으로 한 칸 이동하기

2. 수직으로 두 칸 이동한 뒤에 수평으로 한 칸 이동하기

여기에 더해, 양수와 음수 값을 생각하면 총 8가지의 경우의 수가 나오게 된다.

이 8가지 경로를 하나씩 확인하며 각 위치로 이동이 가능한지 확인한다.

리스트를 이용하여 8가지 방향에 대한 방향 벡터를 정의한다.

### ✔️ 파이썬 답안 예시

---

{% highlight python linenos %}

# 현재 나이트의 위치 입력받기
input_data = input()
row = int(input_data[1])
column = int(ord(input_data[0])) - int(ord('a')) + 1

# 나이트가 이동할 수 있는 8가지 방향 정의
# 2차원 배열을 이용한 방향 벡터 정의 >> dx, dy를 이용하지 않아도 된다.
steps = [(-2, -1), (-1, -2), (1, -2), (2, -1), (2, 1), (1, 2), (-1, 2), (-2, 1)]

# 8가지 방향에 대하여 각 위치로 이동이 가능한지 확인
result = 0
for step in steps:
    # 이동하고자 하는 위치 확인
    next_row = row + step[0]
    next_column = column + step[1]
    # 해당 위치로 이동이 가능하다면 카운트 증가
    if next_row >= 1 and next_row <= 8 and next_column >= 1 and next_column <= 8:
        result += 1

print(result)

{% endhighlight %}


**Notice:** 이 게시물은 한빛미디어의 [이것이 코딩 테스트다](https://www.youtube.com/watch?v=QhMY4t2xwG0) 영상과 교재를 참고하였습니다.
{: .notice--info}