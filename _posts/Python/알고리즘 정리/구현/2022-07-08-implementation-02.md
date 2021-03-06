---
title: "[파이썬 알고리즘] 상하좌우 문제 풀이"
date: 2022-07-08
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

![문제-1]({{ site.url }}{{ site.baseurl }}/assets/images/python/implementation/01-01.png){: .align-center}

![문제-2]({{ site.url }}{{ site.baseurl }}/assets/images/python/implementation/01-02.png){: .align-center}


### 💡 문제 해결 아이디어

---

구현 유형 문제의 대부분은 요구사항대로 충실히 구현하면 된다.

일련의 명령에 따라 개체를 차례때로 이동시킨다는 점에서 시뮬레이션 유형으로도 분류되며 구현이 중요한 대표적인 문제 유형이다.

*코딩 테스트에서의 시뮬레이션, 구현, 완전 탐색 유형은 서로 유사한 점이 많다는 정도로 기억하자*

### ✔️ 파이썬 답안 예시

---

{% highlight python linenos %}

# N 입력받기
n = int(input())
x, y = 1, 1
plans = input().split()

# L, R, U, D에 따른 이동 방향
dx = [0, 0, -1, 1]
dy = [-1, 1, 0, 0]
move_types = ['L', 'R', 'U', 'D']

# 이동 계획을 하나씩 확인
for plan in plans:
    # 이동 후 좌표 구하기
    for i in range(len(move_types)):
        if plan == move_types[i]:
            nx = x + dx[i]
            ny = y + dy[i]
            # 밖에서 초기화하지 않아도 if문 밖에서 참조 가능
    # 공간을 벗어나는 경우 무시
    if nx < 1 or ny < 1 or nx > n or ny > n:
        continue
    # 이동 수행
    x, y = nx, ny

print(x, y)

{% endhighlight %}


### 🕓 시간 복잡도 분석

---

이동 횟수가 N번이라면 시간 복잡도는 O(N)이다.

즉, **연산 횟수는 이동 횟수에 비례**하므로 시간 복잡도는 매우 넉넉한 편이다.


**Notice:** 이 게시물은 한빛미디어의 [이것이 코딩 테스트다](https://www.youtube.com/watch?v=puH2p1CQEg4) 영상과 교재를 참고하였습니다.
{: .notice--info}