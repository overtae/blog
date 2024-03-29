---
title: "SQL Injection"
date: 2023-01-07
categories:
  - Database
tags:
  - Computer Science
  - SQL Injection
excerpt: "SQL Injection이란 무엇일까"
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

## SQL Injection 이란

입력창에 값을 입력한다면, `SELECT * FROM user WHERE userId = 'ID 입력값' and userPw = 'PW 입력값'` 의 질의문이 실행되는 아래와 같은 로그인 화면이 있다고 가정해보자.

<figure style="width: 200px" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/cs/database/si1.png" alt="">
</figure>

위 화면에서, 한 사용자가 악의를 갖고 관리자 계정 로그인을 아래와 같이 시도하였다.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/cs/database/si2.png" alt="">
</figure>

위 질의문 마지막 부분인 `userPw = '' or '' = ''` 를 본다면, 비밀번호 값에는 null 값이 들어갔지만 or 뒤 구문이 `null = null` 로 항상 참이 되면서 결국 질의문 전체가 참이 된다. 따라서 사용자는 관리자 계정의 아이디만 알고 비밀번호를 몰라도 로그인이 가능하다.

이와 같이 SQL Injection은 응용 프로그램 보안 상의 허점을 의도적으로 이용해 악의적인 SQL문을 실행되게 함으로써 **데이터베이스를 비정상적으로 조작**하는 공격 방법이다. 보통 웹 애플리케이션과 데이터베이스가 연동되는 부분인 로그인이나 검색 등에서 시도된다. 로그인(인증 우회)뿐만 아니라 정보 유출이나 데이터 삭제, 변조 등이 가능하다.

## 위험성

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/cs/database/si3.png" alt="">
  <figcaption><a href="https://owasp.org/www-project-top-ten/">owasp.org</a></figcaption>
</figure>

OWASP 에서 발표한 주요 보안 취약점 목록이다. 순위가 내려가긴 했지만, 인젝션은 여전히 상위권인 것을 알 수 있다. 

## 공격 방법

### Error based SQL Injection

: 논리적 에러 이용

가장 대중적인 방법으로 위에서 말한 예시와 같은 방법이다.


### UNION based SQL Injection

: UNION 명령어 이용

UNION은 두 쿼리문의 결과를 연결한다. — [참고](https://learn.microsoft.com/ko-kr/sql/t-sql/language-elements/set-operators-union-transact-sql?view=sql-server-ver16)

이를 이용하여 원하는 정보를 볼 수 있다. 이 때, 두 쿼리문의 결과 테이블은 컬럼 수와 데이터 형이 같아야 한다.

```sql
SELECT name FROM user WHERE id = 123 UNION SELECT password FROM user
```


### Blind SQL Injection — Boolean based

: 에러 메시지 이용

데이터베이스로부터 참과 거짓의 정보만 알 수 있을 때 (데이터베이스 메시지가 보이지 않을 때) 사용한다. 

임의의 숫자와 계속 비교하는 질의를 보내 정보를 얻어낸다.


### Blind SQL Injection — Time based

: 응답 시간 이용

데이터베이스로부터 참과 거짓의 정보도 알 수 없을 때 사용한다.

SLEEP 이나 BENCHMARK 등을 사용해 시간을 지연시켜 데이터베이스의 구조를 얻어낸다.

### Stored Procedure SQL Injection

: 저장 프로시저 이용

Stored Procedure, 저장 프로시저는 쿼리문들의 집합을 말한다. 이 저장 프로시저에 대한 접근 권한을 갖고 특정 명령을 실행 시켜 서버에 피해를 입힌다.

### Mass SQL Injection

: 기존 SQL Injection 의 확장

한번의 공격으로 다량의 데이터베이스가 조작되어 치명적인 피해를 입힌다. 

악성 스크립트를 삽입하여 사이트에 접속한 사용자를 감염시키는 방법으로 사용된다.

## 대응방안

✅ 입력값 검증

✅ Prepared Statement 사용

✅ 데이터베이스 권한 제한

✅ 데이터베이스 에러 메시지 비공개