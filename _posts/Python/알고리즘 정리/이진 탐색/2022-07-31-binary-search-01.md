---
title: "[파이썬 알고리즘] 이진 탐색 알고리즘 개요"
date: 2022-07-31
categories:
  - Python Algorithm
tags:
  - python
  - 알고리즘
  - 이진 탐색
  - 순차 탐색
  - 트리 자료구조
  - 이진 탐색 트리
excerpt: "이진 탐색과 순차탐색, 트리 자료구조에 대해 알아보자"
---

## 이진 탐색 개요

이진 탐색이란 **리스트 내에서 데이터를 매우 빠르게 탐색**하는 알고리즘이다.

이진 탐색에 대해 알아보기 전에 **순차 탐색**이라는 가장 기본적인 탐색 방법에 대해 이해할 필요가 있다.

## 순차 탐색

순차 탐색은 리스트 안에 있는 특정한 데이터를 찾기 위해 **앞에서부터 데이터를 하나씩 차례대로 확인**하는 방법으로 보통 **정렬되지 않은 리스트에서 데이터를 찾아야 할 때 사용**한다.

데이터가 아무리 많아도 시간이 충분하다면 항상 원하는 데이터를 찾을 수 있다는 장점이 있다.

### 💻 파이썬 코드

---

아래는 찾을 문자열이 몇 번째 데이터인지를 출력하는 코드이다.

{% highlight python linenos %}
# 순차 탐색 소스코드 구현
def sequential_search(n, target, array):
    # 각 원소를 하나씩 확인하며
    for i in range(n):
        # 현재의 원소가 찾고자 하는 원소와 동일한 경우
        if array[i] == target:
            return i + 1 # 현재의 위치 반환 (인덱스는 0부터 시작하므로 1 더하기)
    return -1 # 원소를 찾지 못한 경우 -1 반환

print("생성할 원소 개수를 입력한 다음 한 칸 띄고 찾을 문자열을 입력하세요.")
input_data = input().split()
n = int(input_data[0]) # 원소의 개수
target = input_data[1] # 찾고자 하는 문자열

print("앞서 적은 원소 개수만큼 문자열을 입력하세요. 구분은 띄어쓰기 한 칸으로 합니다.")  
array = input().split()

# 순차 탐색 수행 결과 출력
print(sequential_search(n, target, array))
{% endhighlight %}

### 🕒 시간 복잡도

---

데이터 정렬 여부와 상관없이 **가장 앞에 있는 원소부터 하나씩 확인**하므로 데이터의 개수를 N이라고 할 때 최대 N번의 연산이 필요하다. 따라서 최악의 경우 시간 복잡도는  `O(N)`이다.

## 이진 탐색: 반으로 쪼개면서 탐색

이진 탐색은 **배열의 데이터가 정렬되어 있어야만 사용**할 수 있는 알고리즘으로, 탐색 범위를 절반씩 좁혀가며 데이터를 탐색한다는 특징이 있다.

데이터가 무작위라면 사용이 불가능하지만, 이미 정렬되어 있는 경우 **매우 빠르게** 데이터를 찾을 수 있다.

이진 탐색은 위치를 나타내는 변수 3개`(시작점, 끝점, 중간점)`를 사용하며, **찾으려는 데이터와 중간점 위치에 있는 데이터를 반복적으로 비교**해서 원하는 데이터를 찾아내는 과정을 말한다.

### 예시로 알아보는 이진 탐색

이미 정렬이 되어있는 10개의 데이터 중에서 값이 4인 데이터를 찾는 과정을 자세히 알아보자.

1️⃣ **시작점**과 **끝점**을 확인한 후, 둘 사이 **중간점**을 정한다.

> 이때, 중간점이 실수일 경우 소수점 이하를 버린다.

시작점인 [0]과 끝점인 [9]의 중간값인 4.5에서 소수점 이하를 버려 중간점은 [4]이다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/00-01.png){: .align-center}

**중간점의 데이터**와 **찾으려는 데이터**를 비교하여 **끝점**의 위치를 옮긴다.

중간점의 데이터인 8보다 찾으려는 데이터인 4가 작으므로 끝점을 중간점의 이전인 [3]으로 변경한다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/00-02.png){: .align-center}

2️⃣ **시작점**과 **바뀐 끝점** 사이 **중간점**을 다시 정한다.

시작점인 [0]과 끝점인 [3]의 중간값인 1.5에서 소수점 이하를 버려 중간점은 [1]이다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/00-03.png){: .align-center}

3️⃣ **중간점의 데이터**와 **찾으려는 데이터**를 비교하여 **시작점**의 위치를 옮긴다.

중간점의 데이터인 2보다 찾으려는 데이터인 4가 크므로 시작점을 중간점 이후인 [2]로 변경한다.

시작점이 [2], 끝점은 [3]이므로 중간값이 2.5가 되어 중간점이 [2]가 된다.

중간점의 데이터인 4는 찾으려는 데이터와 동일하므로 탐색을 종료한다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/00-04.png){: .align-center}

이와 같이 전체 데이터의 개수는 10개이지만 이진 탐색을 이용하여 총 3번의 탐색으로 데이터를 찾을 수 있었다.

### 🕒 시간 복잡도

---

이진 탐색은 한 번 확인할 때마다 확인하는 원소의 개수가 절반씩 줄어든다는 점에서 시간 복잡도가 `O(logN)`이다.

### 💻 파이썬 코드

---

이진 탐색을 구현할 때, 재귀 함수를 이용하는 방법과 반복문을 이용하는 방법 2가지가 있다.

<details open>
<summary>재귀 함수</summary>
<div markdown="1">

{% highlight python linenos %}
# 이진 탐색 소스코드 구현 (재귀 함수)
def binary_search(array, target, start, end):
    if start > end:
        return None
    mid = (start + end) // 2 # 중간점
    # 찾은 경우 중간점 인덱스 반환
    if array[mid] == target:
        return mid
    # 중간점의 값보다 찾고자 하는 값이 작은 경우 왼쪽 확인
    elif array[mid] > target:
        return binary_search(array, target, start, mid - 1)
    # 중간점의 값보다 찾고자 하는 값이 큰 경우 오른쪽 확인
    else:
        return binary_search(array, target, mid + 1, end)

# n(원소의 개수)과 target(찾고자 하는 값)을 입력 받기
n, target = list(map(int, input().split()))
# 전체 원소 입력 받기
array = list(map(int, input().split()))

# 이진 탐색 수행 결과 출력
result = binary_search(array, target, 0, n - 1)
if result == None:
    print("원소가 존재하지 않습니다.")
else:
    print(result + 1)
{% endhighlight %}

</div>
</details>

<details open>
<summary>재귀 함수</summary>
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
        # 중간점의 값보다 찾고자 하는 값이 큰 경우 오른쪽 확인
        else:
            start = mid + 1
    return None

# n(원소의 개수)과 target(찾고자 하는 값)을 입력 받기
n, target = list(map(int, input().split()))
# 전체 원소 입력 받기
array = list(map(int, input().split()))

# 이진 탐색 수행 결과 출력
result = binary_search(array, target, 0, n - 1)
if result == None:
    print("원소가 존재하지 않습니다.")
else:
    print(result + 1)
{% endhighlight %}

</div>
</details>

## 알아둘 것

백지 상태에서 이진 탐색의 소스코드를 구현하는 것은 상당히 어렵기 때문에 **코드를 암기**하고 있는 것이 많은 도움이 된다고 한다.

추가로, 탐색 범위가 `1,000만`이나 `2,000만`을 넘어간다면 **이진 탐색**으로 문제에 접근해보는 것이 좋다고 한다.

## 트리 자료구조

앞에서 이진 탐색은 데이터가 정렬이 되어있을 때만 사용이 가능하다고 했다.

데이터베이스의 경우 내부적으로 대용량 데이터 처리에 적합한 **트리 자료구조**를 이용하여 ***항상 데이터가 정렬***되어 있다.

따라서 데이터베이스에서의 탐색은 이진 탐색과 유사한 방법을 이용하여 항상 빠른 탐색을 수행하도록 설계되어있다고 한다.

> 여기서 **트리 자료구조**란 무엇일까?

트리 자료구조는 **그래프 자료구조**의 일종으로 데이터베이스 시스템이나 파일 시스템과 같은 곳에서 **많은 양의 데이터를 관리하기 위한 목적**으로 사용한다.

트리 자료구조는 **노드**와 **노드의 연결**로 표현이 가능하다.

**💡 노드 :** 정보의 단위. 어떠한 정보를 가지고 있는 개체
{: .notice}

![image]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/tree-01.png){: .align-center}

아래는 트리 자료구조의 특징들이다.

- 표현 방식 : 부모 노드와 자식 노드의 관계
- 루트 노드 : 트리의 최상단 노드
- 단말 노드 : 트리의 최하단 노드
- 트리에서 일부를 떼어내도 트리 구조이며 이를 서브 트리라 함
- 트리는 파일 시스템과 같이 계층적이고 정렬된 데이터를 다루기에 적합


## 이진 탐색 트리

이제 트리 자료구조를 이용한 이진 탐색에 대해 알아보도록 하자.

이진 탐색 트리란 이진 탐색이 동작할 수 있도록 고안된 효율적인 탐색이 가능한 자료구조를 말한다.

이진 탐색 트리는 이미지와 같이 **왼쪽 자식 노드(17) < 부모 노드(30) < 오른쪽 자식 노드(48)**가 성립해야한다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/tree-02.png){: .align-center}


### 데이터 조회 과정

---

이미 구현되어 있는 이진 탐색 트리에서 찾는 원소가 37일 때의 과정을 살펴보자.

1️⃣ 루트 노드부터 방문하여 루트 노드와 찾는 값을 비교한다.

**루트 노드(30) < 찾는 원소값(37)** 이므로 왼쪽에 있는 모든 노드는 확인할 필요가 없다.

따라서 오른쪽 노드를 방문한다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/tree-search-01.png){: .align-center}

2️⃣ **오른쪽 자식 노드(48) > 찾는 원소값(37)** 이므로 48의 오른쪽 자식 노드는 확인할 필요가 없다.

따라서 왼쪽 노드를 방문한다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/tree-search-02.png){: .align-center}

3️⃣ **현재 방문한 노드(37) == 찾는 원소값(37)** 이므로 탐색을 종료한다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/python/binary-search/tree-search-03.png){: .align-center}

**Notice:** 이 게시물은 한빛미디어의 [이것이 코딩 테스트다](https://book.naver.com/bookdb/book_detail.nhn?bid=16439154) 교재를 참고하였습니다.
{: .notice--info}