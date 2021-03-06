---
title: "[파이썬 알고리즘] 문자열 재정렬 문제 풀이"
date: 2022-07-10
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

![문제]({{ site.url }}{{ site.baseurl }}/assets/images/python/implementation/04-01.png){: .align-center}

![문제]({{ site.url }}{{ site.baseurl }}/assets/images/python/implementation/04-02.png){: .align-center}


### ✏ 나의 풀이

---

{% highlight python linenos %}

s = sorted(input())
h = sum([int(x) for x in s if x.isdigit()])
r = [ss for ss in s if ss.isalpha()] + [str(h)]
print(''.join(r))

{% endhighlight %}

문자가 알파벳인지를 판별해주는 `isalpha()`와 문자가 숫자인지를 판별해주는 `isdigit()`을 사용하여 입력받은 문자열을 나눠주었다.


### 💡 문제 해결 아이디어

---

이 문제 역시 요구사항대로 충실히 구현하면 된다.

문자열이 입력되었을 때 문자를 하나씩 확인한다.

숫자인 경우 따로 합계를 계산하고, 알파벳인 경우 별도의 리스트에 저장한다.

결과적으로 리스트에 저장된 알파벳을 정렬해 출력하고, 합계를 뒤에 붙여 출력하면 정답이다.


### ✔️ 파이썬 답안 예시

---

{% highlight python linenos %}

data = input()
result = []
value = 0

# 문자를 하나씩 확인하며
for x in data:
	# 알파벳인 경우 결과 리스트에 삽입
	if x.isalpha():
		result.append(x)
	# 숫자는 따로 더하기
	else:
		value += int(x)

# 알파벳을 오름차순으로 정렬
result.sort()

# 숫자가 하나라도 존재하는 경우 가장 뒤에 삽입
if value != 0:
	result.append(str(value))

# 최종 결과 출력(리스트를 문자열로 변환하여 출력)
print(''.join(result))

{% endhighlight %}


### 💬 풀이 평가

---

파이썬 내장 함수가 있어 풀이를 쉽게 할 수 있었다. 특히 `isdigit()`과 `isalpha()`와 같은 함수는 꼭 알아두어야겠다.


**Notice:** 이 게시물은 한빛미디어의 [이것이 코딩 테스트다](https://www.youtube.com/watch?v=QhMY4t2xwG0) 영상과 교재를 참고하였습니다.
{: .notice--info}