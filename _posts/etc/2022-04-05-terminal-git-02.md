---
title: "[Git & GitHub] Git 터미널 명령어 2"
date: 2022-04-05
categories:
  - Git
tags:
  - git
exerpt: 간단한 명령어 정리 2
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

### git clone : 저장소 복제

---

```shell
$ git clone https://github.com/[자신의 계정 이름]/[fork한 레포지토리 이름].git
```

`git clone` 뒤 주소의 경우 깃허브 레포지토리에 들어가서 Code 버튼을 누르고 HTTPS 주소를 복사하면 된다.

### 원본 저장소와 동기화

---

```shell
$ git remote add upstream https://github.com/[원본 계정 이름]/[원본 레포지토리 이름].git
```

upstream이란 이름의 remote 저장소를 원본 레포지토리에 추가한다는 의미이다. .. 아마도.

remote 저장소는 네트워크 어딘가에 있는 저장소를 말한다고 한다.

### 변경 내역을 로컬 저장소와 병합

---

```shell
$ git pull upstream main
```

원본 저장소에 있는 내용을 당겨(pull) 가져온다.

### 깃허브 업로드 삼총사

---

```shell
$ git add . 
$ git commit -m "[이름]: 백준_0000번문제 풀이"
$ git push origin main
```

내가 작업한 내용을 업로드하고 PR을 보내면 된다.

### 주의 사항

---

git pull을 이용하여 코드작업 전, 후로 최신화 시켜주기

`pull`은 **원본 레포지토리** 이용, `push`는 **개인 레포지토리** 이용하기