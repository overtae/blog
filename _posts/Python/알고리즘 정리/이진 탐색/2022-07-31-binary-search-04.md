---
title: "[파이썬 알고리즘] 부품 찾기 문제 풀이"
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

![문제]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/03-01.png){: .align-center}

![문제]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/03-02.png){: .align-center}

![문제]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/03-03.png){: .align-center}


### 💡 문제 해결 아이디어

---

1. 매장 내 N개의 부품을 번호 기준으로 정렬
2. M개의 찾고자 하는 부품이 각각 매장에 존재하는지 검사


### ✔️ 파이썬 답안 예시

---

<details open>
<summary>이진 탐색</summary>
<div markdown="1">

{% highlight python linenos %}

# 이진 탐색 소스코드 구현 (반복문)
def binary_search(array, target, start, end):
    while start <= end:
        mid = (start + end) // 2
        # 찾은 경우 중간점 인덱스 반환
        if array[mid] == target:
            return mid
        # 중간점의 값보다 찾고자 하는 값이 작은 경우 왼쪽 확인
        elif array[mid] > target:
            end = mid - 1
        # 중간점의 값보다 찾고자 하는 값이 작은 경우 오른쪽 확인
        else:
            start = mid + 1
    return None

# N(가게의 부품 개수) 입력
n = int(input())
# 가게에 있는 전체 부품 번호를 공백을 기준으로 구분하여 입력
array = list(map(int, input().split()))
array.sort() # 이진 탐색을 수행하기 위해 사전에 정렬 수행
# M(손님이 확인 요청한 부품 개수) 입력
m = int(input())
# 손님이 확인 요청한 전체 부품 번호를 공백을 기준으로 구분하여 입력
x = list(map(int, input().split()))

# 손님이 확인 요청한 부품 번호를 하나씩 확인
for i in x:
    # 해당 부품이 존재하는지 확인
    result = binary_search(array, i, 0, n - 1)
    if result != None:
        print('yes', end=' ')
    else:
        print('no', end=' ')

{% endhighlight %}

</div>
</details>

<details open>
<summary>정렬</summary>
<div markdown="1">

{% highlight python linenos %}

# N(가게의 부품 개수) 입력
n = int(input())
array = [0] * 1000001

# 가게에 있는 전체 부품 번호를 입력 받아서 기록
for i in input().split():
    array[int(i)] = 1

# M(손님이 확인 요청한 부품 개수) 입력
m = int(input())
# 손님이 확인 요청한 전체 부품 번호를 공백을 기준으로 구분하여 입력
x = list(map(int, input().split()))

# 손님이 확인 요청한 부품 번호를 하나씩 확인
for i in x:
    # 해당 부품이 존재하는지 확인
    if array[i] == 1:
        print('yes', end=' ')
    else:
        print('no', end=' ')

{% endhighlight %}

</div>
</details>

<details open>
<summary>set()</summary>
<div markdown="1">

`set()` 함수는 파이썬에서 집합을 표현할 때 사용하는 자료형으로 **특정한 데이터가 존재하는지 검사할 때 매우 효과적**이다.

{% highlight python linenos %}

# N(가게의 부품 개수) 입력
n = int(input())
# 가게에 있는 전체 부품 번호를 입력 받아서 집합(Set) 자료형에 기록
array = set(map(int, input().split()))

# M(손님이 확인 요청한 부품 개수) 입력
m = int(input())
# 손님이 확인 요청한 전체 부품 번호를 공백을 기준으로 구분하여 입력
x = list(map(int, input().split()))

# 손님이 확인 요청한 부품 번호를 하나씩 확인
for i in x:
    # 해당 부품이 존재하는지 확인
    if i in array:
        print('yes', end=' ')
    else:
        print('no', end=' ')

{% endhighlight %}

</div>
</details>


**Notice:** 이 게시물은 한빛미디어의 [이것이 코딩 테스트다](https://book.naver.com/bookdb/book_detail.nhn?bid=16439154) 교재를 참고하였습니다.
{: .notice--info}