---
title: "[파이썬 알고리즘] 떡볶이 떡 만들기 문제 풀이"
date: 2022-07-31
categories:
  - Python Algorithm
tags:
  - python
  - 알고리즘
  - 이진 탐색
excerpt: "이진 탐색 알고리즘을 이용하여 문제를 풀어보자"
---

### ❓ 문제

---

![문제]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/02-01.png){: .align-center}

![문제]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/02-02.png){: .align-center}


### 💡 문제 해결 아이디어

---

적절한 높이를 찾을 때까지 이진 탐색을 수행하여 높이 H를 반복해서 조정하면 된다.

*현재 이 높이로 자르면 조건을 만족할 수 있는가?*를 확인한 후, 조건의 만족 여부에 따라 탐색 범위를 좁혀서 해결이 가능하다.

절단기의 높이는 0부터 10억이다. >> **큰 탐색 범위일 경우 이진 탐색 사용**


### ✔️ 파이썬 답안 예시

---

{% highlight python linenos %}

# 떡의 개수(N)와 요청한 떡의 길이(M)을 입력
n, m = list(map(int, input().split(' ')))
# 각 떡의 개별 높이 정보를 입력
array = list(map(int, input().split()))

# 이진 탐색을 위한 시작점과 끝점 설정
start = 0
end = max(array)

# 이진 탐색 수행 (반복적)
result = 0
while(start <= end):
    total = 0
    mid = (start + end) // 2
    for x in array:
        # 잘랐을 때의 떡볶이 양 계산
        if x > mid:
            total += x - mid
    # 떡볶이 양이 부족한 경우 더 많이 자르기 (오른쪽 부분 탐색)
    if total < m:
        end = mid - 1
    # 떡볶이 양이 충분한 경우 덜 자르기 (왼쪽 부분 탐색)
    else:
        result = mid # 최대한 덜 잘랐을 때가 정답이므로, 여기에서 result에 기록
        start = mid + 1

# 정답 출력
print(result)

{% endhighlight %}


**Notice:** 이 게시물은 한빛미디어의 [이것이 코딩 테스트다](https://www.youtube.com/watch?v=-Gx0T92-7h8) 영상과 교재를 참고하였습니다.
{: .notice--info}