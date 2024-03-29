---
title: "로드밸런싱"
date: 2022-11-19
categories:
  - Network
tags:
  - Computer Science
  - 로드밸런싱
excerpt: "로드밸런싱과 그 기법에 대해 알아보자"
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

## 로드밸런싱

로드밸런싱이란 **서버가 처리해야 할 업무 혹은 요청(Load)을 여러 대의 서버로 나누어(Balancing) 처리**하는 것을 의미한다. 또한 로드밸런싱을 해주는 장치 또는 기술을 **로드밸런서**라고 한다. 한 대의 서버로 부하가 집중되지 않도록 트래픽을 관리해 각각의 서버가 최적의 퍼포먼스를 보일 수 있도록 한다.

### 언제 필요할까?

---

서비스 규모가 커지고, 클라이언트의 수가 늘어나게 된다면 기존 서버만으로는 정상적인 서비스가 불가능하게 될 것이다. 이럴 경우 크게 아래의 두 가지 대처 방법이 있다.

→ Scale-up : 기존 서버 성능 확장

→ Scale-out : 기존 서버와 동일하거나 낮은 성능의 서버를 증설

이 중 로드밸런싱은 Scale-out 방식, 즉 **여러 대의 서버**를 두고 서비스를 제공하는 분산 처리 시스템에서 필요한 기술이다.

## 로드밸런서 종류

로드밸런서의 종류는 [OSI 7 계층](https://overtae.github.io/blog/network/osi-7-layer/)에 따라 나뉘며, 상위 계층으로 갈수록 더욱 정교한 로드밸런싱이 가능하다. 주로 **L4 로드밸런서**와 **L7 로드밸런서**가 많이 활용되는데, L4부터 포트 정보를 바탕으로 트래픽을 분산하는 것이 가능하기 때문이다.

### L4 로드밸런서

---

4 계층인 전송 계층에서 TCP/UDP 포트 정보를 바탕으로 트래픽을 분산한다. *IP 주소, 포트 번호, MAC 주소, 전송 프로토콜*에 따라 트래픽을 나누는 것이 가능하다. 

#### — 장점

👉 데이터 안을 들여다보지 않고 패킷 레벨에서만 로드를 분산하기 때문에 속도가 빠르고 효율이 높다.

👉 데이터의 내용을 복호화 할 필요가 없기에 안전하다.

👉 L7 로드밸런서보다 가격이 저렴하다.

#### — 단점

👉 패킷의 내용을 살펴볼 수 없기 때문에 섬세한 라우팅이 불가능하다.

👉 사용자의 IP가 수시로 바뀌는 경우라면 연속적인 서비스를 제공하기 어렵다.

### L7 로드밸런서

---

7 계층인 애플리케이션 계층에서 TCP/UDP 정보, HTTP의 URI, FTP의 파일명, 쿠키 정보 등을 바탕으로 트래픽을 분산한다. HTTP 헤더, 쿠키 등과 같은 *사용자의 요청을 기준으로 특정 서버에 트래픽을 분산하는 것*이 가능하다.

#### — 장점

👉 클라이언트의 요청을 세분화하여 서버에 전달할 수 있다.

👉 상위 계층에서 로드를 분산하기 때문에 훨씬 더 섬세한 라우팅이 가능하다.

👉 캐싱 기능을 제공한다.

👉 비정상적인 트래픽(DoS/DDoS)을 사전에 필터링 할 수 있어 서비스 안정성이 높다.

#### — 단점

👉 패킷의 내용을 복호화해야 하기에 더 높은 비용을 지불해야 한다.

👉 클라이언트가 로드밸런서와 인증서를 공유해야 하기 때문에 공격자가 로드밸런서를 통해서 클라이언트 데이터에 접근할 (보안 상의) 위험성이 존재한다.

## 로드밸런싱 알고리즘

로드밸런싱 알고리즘에는 여러가지가 있는데, 서버의 상황에 맞춰 적절한 방법을 선택해야 한다. 크게 정적과 동적으로 나누어 알아보자.

### ☝️ 정적 로드밸런싱

---

정적 로드밸런싱 알고리즘은 고정된 규칙을 따르며 **현재 서버 상태와 무관**하다.

#### — 라운드 로빈 방식

---

서버에 들어온 요청을 순서대로 돌아가며 배정한다.

✅ 여러 대의 동일한 스펙을 가진 서버를 가진 경우 적합하다.

✅ 서버와의 연결(세션)이 오래 지속되지 않는 경우 적합하다.

#### — 가중 라운드 로빈 방식

---

각 서버마다 가중치를 매기고 가중치가 높은 서버에 클라이언트 요청을 우선적으로 배분한다. 가중치는 우선순위 또는 용량에 따라 할당할 수 있다. 

✅ 서버의 트래픽 처리 능력이 다른 경우 적합하다.

#### — IP 해시 방식

---

클라이언트의 IP 주소를 해싱해 특정 서버로 매핑하여 요청을 처리한다. 

✅ 사용자가 항상 동일한 서버로 연결되는 것을 보장한다.

**💡 해싱?** Hashing, 임의의 길이를 지닌 데이터를 고정된 길이의 데이터로 매핑하는 것, 또는 그러한 함수
{: .notice--success}

### ✌️ 동적 로드밸런싱

---

동적 로드밸런싱 알고리즘은 트래픽을 분배하기 전에 **서버의 현재 상태를 검사**한다.

#### — 최소 연결 방식

---

요청이 들어온 시점에 가장 적은 연결 상태를 보이는 서버에 우선적으로 트래픽을 배분한다.

✅ 세션이 자주 길어지는 경우 적합하다.

✅ 서버에 분배된 트래픽들이 일정하지 않은 경우에 적합하다.

#### — 가중 최소 연결 방식

---

일부 서버가 다른 서버보다 더 많은 트래픽을 처리할 수 있다고 가정한다. 각 서버에 다른 가중치나 용량을 할당할 수 있고, 용량 별 연결이 가장 적은 서버로 트래픽을 우선적으로 배분한다.

#### — 최소 응답 시간 방식

---

서버의 현재 연결 상태와 응답 시간을 모두 고려하여, 가장 적은 연결 상태와 가장 짧은 응답 시간을 보이는 서버에 트래픽을 우선적으로 배분한다.

#### — 리소스 기반 방식

---

현재 서버 부하를 분석하여 트래픽을 배분한다. 각 서버에서는 서버 리소스의 사용량을 계산하는 에이전트가 실행되고, 트래픽 배분 전에 에이전트에 충분한 리소스가 있는지 확인한다.