---
title: "Blocking, Non-blocking"
date: 2022-11-23
categories:
  - Network
tags:
  - Computer Science
  - 로드밸런싱
excerpt: "프로세스의 유휴 상태"
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

## 개요

블로킹과 논블로킹은 주로 동기와 비동기 개념과 함께 비교되며, 서술된다. 바로 아래와 같은 조합으로 사용되기 때문인데, 이번 포스팅에서는 블로킹과 논블로킹, 동기와 비동기 개념과 함께 아래 조합들도 조금씩 살펴볼 예정이다. 살펴보기에 앞서 한 가지 머리에 입력해야 될 것은, 블로킹/논블로킹은 동기/비동기와 다른 개념이라는 것이다.

<figure style="width:280px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/cs/network/bnb-1.png" alt="">
  <figcaption><a href="https://developer.ibm.com/articles/l-async/">https://developer.ibm.com/articles/l-async/</a></figcaption>
</figure>

## Blocking, Non-blocking

프로세스의 유휴 상태에 대한 개념으로, **제어권**을 바로 반환하는지 갖고 있는지가 관심사이다.

**💡 유휴 상태?** 어떠한 프로그램에 의해서도 사용되지 않는 상태 — [참고](https://ko.wikipedia.org/wiki/%EC%9C%A0%ED%9C%B4_(CPU))
{: .notice--success}

### Blocking

: 자신의 작업을 진행하다가 다른 주체의 작업이 시작되면 다른 작업이 끝날 때까지 기다렸다가 자신의 작업을 시작하는 것

---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/cs/network/bnb-2.png" alt="">
</figure>

1️⃣ A 함수가 B 함수를 호출하며 B 함수에게 제어권을 넘긴다.

2️⃣ B 함수는 제어권을 갖고 함수를 실행한다.

  이때, A 함수는 제어권이 없는 상태가 되며 다른 일을 할 수 없다.

3️⃣ B 함수의 실행이 끝나면 A 함수에게 제어권을 돌려준다.

정리해보자면, 호출된 함수는 자신의 작업이 종료되기 전까지 *호출한 함수에게 제어권을 돌려주지 않는다.*

### Non-blocking

: 다른 주체의 작업에 관련 없이 자신의 작업을 하는 것

---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/cs/network/bnb-3.png" alt="">
</figure>

1️⃣ A 함수가 B 함수를 호출하고, B 함수가 실행되지만 제어권을 바로 돌려준다.

2️⃣ A 함수는 제어권을 갖고 있기 때문에 자신의 작업을 계속 한다.

정리하자면, 호출된 함수의 *작업 완료 여부에 상관 없이* 호출한 함수는 다른 일을 진행할 수 있다.

## Synchronous, Asynchronous

프로세스의 작업 순서 보장에 대한 개념으로, **함수의 작업 완료 여부를 확인하는지**에 따라 **작업 순서**를 보장 하는가가 관심사이다.

<details open>
<summary>📝 토막 상식</summary>
<div markdown="1">

✏️ **syn** — together

✏️ **chrono** — time

✅ Synchronous: 함께 시간을 맞춘다.

✅ Asynchronous (부정형): 시간을 맞추지 않는다.

</div>
</details>
    

### 동기, Synchronous

: 현재 작업의 처리가 끝나는 시간에 맞춰 다음 작업을 요청하는 것

---


<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/cs/network/bnb-4.png" alt="">
</figure>

각 작업의 주체들은 **끝나는 시간과 시작하는 시간**(왼쪽)을 맞추거나, **시작하는 시간과 끝나는 시간**(오른쪽)을 맞춘다.

✅ 호출된 함수가 결과를 반환할 때까지 대기

✅ 호출한 함수의 리턴값과 **작업 완료 여부를 계속 확인하며 신경씀**

### 비동기, Asynchronous

: 현재 작업의 응답이 끝나지 않은 상태에서 다음 작업을 요청하는 것

---

<figure style="width:280px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/cs/network/bnb-5.png" alt="">
</figure>

작업을 수행하는 주체의 시작과 끝나는 시간에 관계없이 각자 별도의 시작과 끝나는 시간을 지닌다. 즉, 서로의 시작과 종료 시간이 영향을 주지 않는다.

✅ 호출한 함수는 호출된 함수의 **작업 완료 여부에 신경쓰지 않음**

  — *작업 요청 시 요청의 결과값을 간접적으로 받는데, 이는 return 값과 다를 수 있다.*

✅ 함수를 호출할 때 콜백 함수를 전달하여 호출된 함수의 작업이 완료되었다면 콜백 함수를 실행

**💡 콜백 함수?** 다른 함수의 인자로 들어간 함수로서 특정 시점에 실행되는 함수로, **작업 완료 여부를 신경**쓴다.
{: .notice--success}

## 조합

위에서 알아본 각각의 특징들로 조합을 해본다면, 아래와 같겠다.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/cs/network/bnb-6.png" alt="">
</figure>

이때, Async-blocking(왼쪽 아래)의 경우 Sync-blocking(왼쪽 위)과 성능 차이가 거의 없어 사용하는 경우가 거의 없다고 한다.