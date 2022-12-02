---
title: "HTTP GET vs. POST"
date: 2022-11-11
categories:
  - Network
tags:
  - Computer Science
  - HTTP
  - GET
  - POST
excerpt: "HTTP 요청 방식 중 GET과 POST 방식의 비교"
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

## 개요

클라이언트가 서버에 요청을 보내면 서버는 요청에 따른 응답을 전송한다. 이 때 클라이언트는 HTTP 프로토콜을 통해 HTTP 요청을 보내게 되고, HTTP 요청 방식 중 대표적인 것이 바로 GET과 POST 방식이다. 이 둘의 특징을 비교하여 알아보도록 하자.

## GET

GET은 데이터를 읽을 때, 즉 **서버로부터 데이터를 조회**하기 위해 설계된 메소드이다. 요청 URL 끝에 쿼리스트링으로 요청에 필요한 데이터를 전송하게 된다. 


**💡 쿼리스트링:** ? 뒤에 이름, 값 쌍으로 이루어진 문자열이다. 파라미터가 여러 개일 경우 & 기호를 사용해 연결한다.<br/>ex) example.com/param?name1=value1&name2=value2
{: .notice--success}

### 특징

---

👉 캐시가 가능하다.

  └─ 동일한 요청 발생 시 캐시된 데이터를 사용한다.

👉 브라우저 기록에 남는다.

👉 북마크에 추가할 수 있다.

👉 중요한 데이터 처리에는 사용하면 안된다.

  └─ 요청 URL에 값이 노출되기 때문이다.

👉 요청 (쿼리스트링) 길이에 제한이 있다.

  └─ 최대 2048 Byte

👉 데이터를 요청하는 데에만 사용된다.

  └─ 수정이 불가하다.

## POST

POST는 서버에 데이터를 보내 **리소스를 생성하거나 변경**하기 위해 설계된 메소드이다. GET과 달리 HTTP 요청 Body에 필요한 데이터를 담아 전송한다. 

### 특징

---

👉 캐시되지 않는다.

👉 브라우저 기록에 남지 않는다.

👉 북마크에 추가할 수 없다.

👉 GET 보다는 상대적으로 높은 보안성을 제공한다.

  └─ 크롬 개발자 도구 등으로 요청 내용을 확인할 수 있기 때문에 중요한 데이터는 암호화가 필요하다.

👉 요청 데이터 크기에 제한이 없다.

## GET vs. POST

| -                          | GET                                     | POST                                                         |
| -------------------------- | --------------------------------------- | ------------------------------------------------------------ |
| 설계 목적                  | 서버에 있는 정보를 가져오기 위해 설계   | 서버로 정보를 전달하기 위해 설계                             |
| 캐시 저장                  | 가능                                    | 불가능                                                       |
| 북마크                     | 가능                                    | 불가능                                                       |
| 전달할 수 있는 데이터 크기 | 최대 2048 Byte                          | 제한 없음                                                    |
| 보안성                     | 요청 URL에 값 노출, 보안 문제 발생 가능 | URL에 전달하려는 정보 값 노출 X, 상대적으로 높은 보안성 제공 |
| 멱등성                     | 멱등하다.                               | 멱등하지 않다.                                               |
{: .wide-table}

**💡 멱등성?** 동일한 요청을 여러 번 전송하더라도 원래 의도한 바와 같이 동일하게 작동하는 속성
{: .notice--success}
