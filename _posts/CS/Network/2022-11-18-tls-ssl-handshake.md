---
title: "TLS/SSL Handshake"
date: 2022-11-18
categories:
  - Network
tags:
  - Computer Science
  - TLS
  - SSL
excerpt: "암호화 된 데이터 전달 과정"
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

## SSL/TLS ?

HTTPS는 SSL/TLS 전송 기술을 사용한다. 여기서 SSL과 TLS는 브라우저와 서버 간의 인증 및 데이터 암호화를 제공하는 암호화 프로토콜로 아래와 같은 뜻을 갖고 있다.

— SSL : Secure Socket Layer

— TLS : Transport Layer Security

두 단어 다 Secure과 Layer가 들어가 있는 것을 알 수 있다. 즉, SSL/TLS 전송 기술은 일반적인 인터넷 통신 (ex. TCP, UDP)에 안전한 계층(Layer)을 추가하는 방식이다. 이를 구현하려면 웹 서버에 SSL/TLS 인증서를 설치해야 한다. TLS는 SSL의 개선 버전인데, 편의상 SSL 인증서라고 쭉 부르고 있다고 한다. 

*~~개선 버전인데 이름이 다른 이유는 SSL을 개발한 Netscape사가 업데이트에 참여하지 않게 되어 소유권 변경을 위해서였다고 한다..~~*

## SSL/TLS 인증서

SSL/TLS 인증서는 SSL/TLS 프로토콜을 사용하여 웹 브라우저가 웹 사이트에 대해 암호화된 네트워크 연결을 확인하고 설정할 수 있게 해준다.

### 이점

---

👉 개인 데이터 보호

: 브라우저와 웹 사이트 간의 모든 통신을 암호화

👉 사용자 신뢰 강화

: 사용자는 SSL/TLS 보호를 통해 데이터가 보호되고 있음을 인지 

👉 규제 준수 지원

: 데이터 기밀성 및 보호에 대한 업계 규정 준수

👉 SEO 개선

: 검색 엔진에서 SSL/TLS 인증서가 없는 웹 사이트보다 더 높은 순위 차지

### 주요 원칙

---

👉 암호화

: 공개키 암호화 방식을 사용한 데이터 암호화

👉 인증

: 웹 서버의 신원을 신뢰할 수 있는지 확인

👉 디지털 서명

: 전송되는 인증서를 조작하지 않았는지 확인 (무결성)

## TLS/SSL 작동방식

TSL/SSL은 클라이언트와 서버간에 handshake를 통해 인증이 이루어진다.

### TLS/SSL Handshake 과정

---

통신을 하는 브라우저와 웹 서버가 서로 암호화 통신을 시작할 수 있도록 신분을 확인하고 필요한 정보를 클라이언트와 서버가 주고받는 과정이다.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/cs/network/tls-handshake.png" alt="">
  <figcaption><a href="https://www.catchpoint.com/blog/wireshark-tls-handshake">catchpoint.com</a></figcaption>
</figure>


#### ClientHello

---

클라이언트가 서버에 연결을 시도하며 전송하는 패킷으로 아래와 같은 정보를 보낸다.

👉 클라이언트가 사용 가능한 SSL (or TLS) 버전 정보

👉 클라이언트가 지원하는 암호화 방식 목록 (Cipher Suite)

👉 클라이언트가 생성한 임의의 난수 (Random Byte 문자열, 이하 randC)

👉 이전에 SSL Handshake를 한 경우, 그때 생성된 Session ID

: Session ID가 있다면, 위 과정을 반복하지 않는다.

👉 기타 extension 정보


**💡 Cipher Suite?** 안전한 키 교환, 전달 대상 인증, 암호화 알고리즘, 메시지 무결성 확인 알고리즘을 위해 사용하는 방식을 묶어 놓은 것
{: .notice--success}

#### ServerHello

---

클라이언트의 패킷을 받은 서버는 아래와 같은 정보를 보낸다.

👉 클라이언트가 보낸 Cipher Suite 중 서버가 선택한 하나

👉 서버의 공개키가 담긴 SSL 인증서 (CA의 비밀키로 암호화 됨)

👉 서버가 생성한 임의의 난수 (이하 randS)


**💡 CA?** 인증 기관으로 웹 소유자, 웹 호스팅 회사 또는 기업에 SSL/TLS 인증서를 판매하는 조직
{: .notice--success}

#### Certificate

---

클라이언트는 서버의 SSL 인증서를 CA 공개키로 복호화함으로써 CA가 발급한 것이 맞는지 확인한다.

#### ServerKeyExchange

---

서버의 공개키가 SSL 인증서 내부에 없는 경우 서버가 직접 전달한다. 만약 공개키가 SSL 인증서 내부에 있다면 생략된다.

#### ServerHelloDone

---

서버가 행동을 마쳤다는 것을 전달한다.

#### ClientKeyExchange

---

클라이언트는 ransC와 randS를 조합하여 premaster secret을 생성한다. 이를 SSL 인증서 내부에서 추출한 서버의 공개키로 암호화하여 서버에 전달한다. 

서버는 premaster secret 을 복호화한 값을 master secret 값으로 저장한다. 이를 사용하여 대칭키 암호화에 사용할 Session key를 생성한다. 즉, 이 Session key로 주고받는 데이터를 암호화하고 복호화하게된다.

#### ChangeCipherSpec

---

클라이언트와 서버가 서로에게 보내는 패킷으로 교환할 정보를 모두 교환한 뒤 통신할 준비가 다 되었음을 알린다.

#### Finished

---

SSL Handshake를 종료한다.