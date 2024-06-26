---
title: "TCP 3, 4 way handshake"
date: 2022-11-06
categories:
  - Network
tags:
  - Computer Science
  - TCP 
  - 3 way handshake
  - 4 way handshake
excerpt: "TCP 연결 생성과 해제 방식"
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

## 개요

이전 포스팅에서 TCP는 **3 way handshake로 연결을 생성**하고 **4 way handshake로 해제**한다는 것을 알게되었다. 이번에는 이 둘에 대해 조금 더 자세하게 알아보도록 하자.

..그 전에 포트 상태 정보와 플래그 정보 먼저 알아보자.

## PORT State 정보

| CLOSED      | 포트가 닫힌 상태                                                                      |
| LISTEN      | 포트가 열린 상태, 연결 요청 대기 중                                                   |
| SYN_RECV    | SYNC 요청을 받고 상대방의 응답을 기다리는 중                                          |
| ESTABLISHED | 포트 연결 상태                                                                        |
| TIME_WAIT   | 서버로부터 FIN을 수신하더라도 일정 시간동안 세션을 남겨놓고 잉여 패킷을 기다리는 과정 |

## Flag 정보

TCP 헤더에는 CONTROL BIT(플래그 비트)가 존재하며, 각각의 비트는 `URG-ACK-PSH-RST_FIN`을 의미한다.

| URG<br/>Urgent                          | 긴급히 처리 하라는 의미, URG가 설정이 되어있다면 해당 데이터부터 처리.                                                         |
| ACK<br/>Acknowledgement                 | 응답 확인, 패킷을 받았다는 것을 의미. 클라이언트가 보낸 최초의 SYN 패킷 이후에 전송되는 모든 패킷은 이 플래그가 설정되어야 함. |
| PSH<br/>Push                            | Push 함수, 메모리 자원이 다 차지 않아도 데이터를 던져줌. 주로 URG와 함께 사용.                                                 |
| RST<br/>Reset                           | 현재 세션에 의해 할당된 메모리 자원을 Null 상태로 만들어 시스템에 반납하라는 의미                                              |
| SYN<br/>Synchronization Sequence Number | 연결 설정 요구, Synchronization Sequence Number(동기화 순서 번호)를 랜덤으로 설정하여 세션 연결, 초기에 Sequence Number 전송   |
| FIN<br/>Finish                          | 연결 해제, 세션 연결 종료, 더 이상 전송할 데이터가 없음을 의미 (4 way handwhake에서 사용)                                      |

## TCP 3 way handshake

TCP 통신 이용시, 데이터를 전송하기 위해 **네트워크 연결을 설정하는 과정**으로 3번의 확인 과정을 거친다고 해서 3 way handshake 라고 부른다. 즉, 데이터를 전송하기 전에 먼저 **정확한 전송을 보장**하기 위해 상대방 컴퓨터와 사전에 세션을 수립하는 과정이다.

### 동작 방식

---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/cs/network/handshake-01.png" alt="">
  <figcaption><a href="http://www.tcpipguide.com/free/t_TCPConnectionEstablishmentProcessTheThreeWayHandsh-3.htm">tcpipguide.com</a></figcaption>
</figure>

영어를 잘 몰라서 그런지 눈에 잘 들어오지는 않는다. 정리하자면 아래와 같다.

우선, 처음에는 클라이언트와 서버 모두 CLOSED 상태이다.

#### 1. 클라이언트 → SYN → 서버

클라이언트가 Sequence Number를 임의의 숫자로 지정하고, SYN 플래그를 1로 설정한 세그먼트 전송

- 클라이언트 : SYN+ACK 응답을 기다리는 SYN_SENT 상태
- 서버 : LISTEN 상태

#### 2. 서버 → SYN+ACK → 클라이언트

서버는 SYN을 받고, ACK Number 필드를 `Sequence Number + 1` 로 지정하고 ACK와 SYN 플래그를 1로 설정한 세그먼트 전송

- 클라이언트 : CLOSED 상태
- 서버 : ACK 응답을 기다리는 SYN_RECEIVED 상태

#### 3. 클라이언트 → ACK → 서버

클라이언트는 SYN+ACK 패킷을 받고, ACK(`Sequence Number + 1`) 패킷을 전송

- 클라이언트 : ESTABLISHED 상태
- 서버 : ESTABLISHED 상태 (포트 연결 상태)

이후엔 연결이 이루어져 데이터가 오가게 된다.

> 왜 Sequence Number를 임의의 숫자로 지정할까?

이전에는 포트 번호를 재사용함에 따라 과거에 사용된 포트 번호 쌍을 사용하는 가능성이 존재했다고 한다. SYN이 난수가 아니라면 서버에서 이전 연결로부터 오는 패킷으로 인식할 수 있기 때문에 난수로 설정한다.

## TCP 4 way handshake

TCP 통신 이용시, **연결을 해제할 때 사용**하는 과정으로 4번의 확인 과정을 거친다고 해서 4 way handshake 라고 부른다.

### 동작 방식

---

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/cs/network/handshake-02.png" alt="">
  <figcaption><a href="http://www.tcpipguide.com/free/t_TCPConnectionTermination-2.htm">tcpipguide.com</a></figcaption>
</figure>

역시 영어라 알기 어렵다. 정리하면 아래와 같다.

#### 1. 클라이언트 → FIN → 서버

클라이언트가 서버에게 연결을 종료한다는 FIN 플래그를 전송

#### 2. 서버 → ACK → 클라이언트

서버는 FIN을 받고, ACK Number 필드를 `Sequence Number + 1` 로 지정하고 ACK 플래그 비트를 1로 설정한 세그먼트 전송, 서버 자신의 통신이 끝날 때까지 대기 (TIME_WAIT)

- 서버 : ACK 전송 이후 CLOSE_WAIT 상태, 아직 남은 데이터가 있다면 마저 전송 후 `close()` 호출
- 클라이언트 : ACK 받은 이후 FIN_WAIT_2 상태, 서버가 FIN 패킷을 보낼 때까지 대기

#### 3. 서버 → FIN → 클라이언트

서버는 데이터를 모두 보냈다면 클라이언트에게 FIN 패킷 전송

- 서버 : FIN 전송 이후 LASK_ACK 상태, 승인 번호 대기

#### 4. 클라이언트 → ACK → 서버

클라이언트는 FIN을 받고, 서버에게 ACK 전송

- 클라이언트 : TIME_WAIT 상태, 일정 시간 이후 CLOSED 상태

> TIME_WAIT?

서버에서 FIN을 전송하기 전에 전송한 패킷이 라우팅 지연이나 패킷 유실로 인한 재전송 등으로 인해 FIN 패킷보다 늦게 도착하는 상황이 발생할 수 있다. 이를 대비하기 위해 클라이언트는 일정 시간동안 세션을 남겨놓고 잉여 패킷을 기다리는 과정인 TIME_WAIT을 거친다. TIME-WAIT이 없다면 패킷의 손실이 발생하거나 통신자 간 연결 해제가 제대로 이루어지지 않을 수 있다.

이를 통해 연결 종료 과정이 연결 설정 과정보다 한 단계 더 많은 이유는 혹시 모를 남아있을 데이터 때문이라는 것을 알 수 있겠다.

## 번외

연결을 설정하고 해제하는 과정에 대해 재밌게 설명한 글이 있어서 가져와봤다.

### 3 way handshake

---

👩 1단계 : 내 말 들려?

🧑 2단계 : 들려. 너도 내 말 들려?

👩 3단계 : 들려.

### 4 way handshake

---

👩 1단계 : 연결 끊고 싶어.

🧑 2단계 : 확인했어. 보내던 거 마저 보낼게.

🧑 3단계 : 다 보냈다. 연결 끊자.

👩 3단계 : 응.


**Notice:** 이 게시물은 [velog.io/@averycode](https://velog.io/@averycode/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-TCPUDP%EC%99%80-3-Way-Handshake4-Way-Handshake), [hyemsinabro.tistory.com](https://hyemsinabro.tistory.com/m/157) 사이트를 참고하였습니다.
{: .notice--info}
