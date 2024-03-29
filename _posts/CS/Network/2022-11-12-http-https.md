---
title: "HTTP vs. HTTPS"
date: 2022-11-12
categories:
  - Network
tags:
  - Computer Science
  - HTTP
  - HTTPS
excerpt: "문서 전송 통신 규약"
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

## Hyper Text Transfer Protocol, HTTP

HTTP란 html 문서 같은 자원을 보내고 받기 위한 표준 프로토콜(통신 규약)이다. — [참고](https://developer.mozilla.org/ko/docs/Web/HTTP)

기본으로 80번 포트를 사용하여 요청을 주고 받으며 무상태 프로토콜이다.

> 무상태 프로토콜?

각각의 데이터 요청이 서로 독립적으로 관리가 되는 프로토콜이라는 의미이다.

클라이언트-서버 모델을 따르기 때문에 클라이언트가 요청을 생성하기 위한 연결을 연 다음, 응답을 받을 때까지 대기한다.

암호화 되지 않은 데이터를 전송하는 프로토콜이기 때문에 **제 3자가 정보를 조회**할 수 있다.

## HTTP Secure, HTTPS

HTTP 프로토콜의 암호화된 버전이 바로 HTTPS이다. — [참고](https://developer.mozilla.org/ko/docs/Glossary/https)

정보를 암호화하는 **SSL(Secure Socket Layer)** 프로토콜을 이용하여 데이터를 주고 받는다고 한다.

따라서 금융 활동과 같이 보안이 필요한 정보를 서버와 안전하게 주고 받을 때 사용한다.

http를 암호화 하는 방식에 따라 https 서버에 요청을 하려면 공개키가 필요하다.

암호화 방식에 대해 간단히 살펴보자면, 암호화와 복호화가 가능한 개인키, 공개키가 있다.

그 중 공개키는 말 그대로 모두에게 공개하여 저장소에 등록한다.

- 개인키로 암호화 > 공개키로 복호화
- 공개키로 암호화 > 개인키로 복호화

자세한 알고리즘의 경우 [이 블로그](https://cryptocat.tistory.com/m/3)를 참고하길 바란다.

## 마치며

https가 보안 관련된 규약이라는 것을 알았으니,

http는 단순 정보 조회에, https는 중요한 정보 처리에 이용하면 되겠다.