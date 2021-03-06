---
title: "[파이썬 알고리즘] 정렬된 배열에서 특정 수의 개수 구하기 문제 풀이"
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

![문제]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/01-01.png){: .align-center}

![문제]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/01-02.png){: .align-center}


### 💡 문제 해결 아이디어

---

**선형 탐색**을 사용할 경우 <span style="color:#f55442">**시간 초과 판정**</span>을 받는다.

데이터가 정렬되어 있기 때문에 **이진 탐색을 수행**하면 된다.

특정 값이 등장하는 첫 번째 위치와 마지막 위치를 찾아 위치 차이를 계산해 문제를 해결할 수 있다.


### ✔️ 파이썬 답안 예시

---

{% highlight python linenos %}

from bisect import bisect_left, bisect_right

# 값이 [left_value, right_vlaue]인 데이터의 개수를 반환하는 함수
def count_by_range(array, left_value, right_value):
	right_index = bisect_right(array, right_value)
	left_index = bisect_left(array, left_value)
	return right_index - left_index

n, x = map(int, input().split()) # 데이터의 개수 N, 찾고자 하는 값 x 입력받기
array = list(map(int, input().split())) # 전체 데이터 입력받기

# 값이 [x, x] 범위에 있는 데이터의 개수 계산
count = count_by_range(array, x, x)

# 값이 x인 원소가 존재하지 않는 다면
if count == 0:
	print(-1)
# 값이 x인 원소가 존재한다면
else:
	print(count)

{% endhighlight %}


**Notice:** 이 게시물은 한빛미디어의 [이것이 코딩 테스트다](https://www.youtube.com/watch?v=-Gx0T92-7h8) 영상과 교재를 참고하였습니다.
{: .notice--info}