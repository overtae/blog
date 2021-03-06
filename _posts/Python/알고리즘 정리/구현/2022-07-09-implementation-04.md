---
title: "[파이썬 알고리즘] 시각 문제 풀이"
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

![문제]({{ site.url }}{{ site.baseurl }}/assets/images/python/implementation/03-01.png){: .align-center}


### 💡 문제 해결 아이디어

---

가능한 모든 시각의 경우를 하나씩 모두 세어 풀 수 있는 문제이다.

하루는 **86,400초**(`24 * 60 * 60`)이므로, 00시 00분 00초부터 23시 59분 59초까지의 모든 경우는 86,400가지이다.

파이썬은 1초에 약 2천만번 정도의 연산을 수행한다고 가정하므로 86,400가지는 컴퓨터에겐 많은 수가 아니다.

따라서 단순히 시각을 1씩 증가시키면서 3이 하나라도 포함되어 있는지를 확인하면 된다.

이러한 유형은 완전 탐색(Brute Forcing) 문제 유형이라 불린다.


### ✔️ 파이썬 답안 예시

---

{% highlight python linenos %}

# H를 입력받기
h = int(input())

count = 0
for i in range(h + 1):
    for j in range(60):
        for k in range(60):
            # 매 시각 안에 '3'이 포함되어 있다면 카운트 증가
            if '3' in str(i) + str(j) + str(k):
                count += 1

print(count)

{% endhighlight %}


**Notice:** 이 게시물은 한빛미디어의 [이것이 코딩 테스트다](https://www.youtube.com/watch?v=QhMY4t2xwG0) 영상과 교재를 참고하였습니다.
{: .notice--info}