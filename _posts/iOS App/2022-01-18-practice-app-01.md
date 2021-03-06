---
permalink: /autolayout/iosapp-02/
classes: wide
title: "[앱 개발] 간단한 테이블 뷰 앱 만들어보기 1"
date: 2022-01-18
categories:
  - iOS App
tags:
  - swift
  - iOS
  - table view
  - navigation controller
excerpt: "테이블뷰와 네비게이션 컨트롤러를 추가하고 기본 기능 구현하기"
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

### 개요

---

테이블 뷰와 네비게이션 컨트롤러를 사용해 목록을 추가, 삭제하는 앱을 만들어 볼 것이다.

목록 중 하나를 누르면 해당 목록의 디테일 페이지로 넘어가고 네비게이션 바의 Add 버튼을 누르면 목록 추가 페이지로 넘어가게 된다.

### Table View Controller 추가

---

처음 화면이 테이블 뷰 화면일 경우 뷰 컨트롤러를 삭제하고 테이블 뷰 컨트롤러를 추가해준다.

그리고 ViewController.swift 파일도 삭제해준다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac1.png){: .align-center}

### Navigation Controller 추가

---

추가하고 싶은 뷰를 꼭 선택한 뒤 [Editor] - [Embed In] - [Navigation Controller]를 선택해준다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac2.png){: .align-center}

#### 시작 화면 지정

시작 화면으로 지정되어있던 View Controler를 삭제했으니 네비게이션 컨트롤러로 시작 화면을 다시 지정해준다.

Is Initial View Controller의 체크박스를 체크해주면 된다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac3.png){: .align-center}

지정해주었다면 왼쪽에 화살표가 생긴다.

### 바 버튼 추가

---

목록을 추가하는 페이지로 넘어가기 위한 버튼을 추가해준다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac4.png){: .align-center}

iOS에 기본적으로 있는 Add 아이콘을 사용하였다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac5.png){: .align-center}

### 세그웨이 연결

---

스토리보드에 뷰 컨트롤러를 하나 추가하고 Add 버튼과 연결해준다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac6.png){: .align-center}

뷰 컨트롤러를 하나 더 추가하고 Table View의 Cell과 연결해준다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac7.png){: .align-center}

#### Scene 이름 지정

어떤 화면인지 알 수 있게 이름을 지정해주었다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac8.png){: .align-center}

### Add Scene

---

추가할 목록을 입력받을 텍스트 필드와 추가 버튼을 배치해준다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac10.png){: .align-center}

#### Tip

[Editor] - [Canvas] - [Bounds Rectangles] 옵션을 체크해주면 각각의 테두리가 보여 디자인하기 쉬워진다.

### Detail Scene

---

선택한 목록의 설명을 보여줄 레이블을 배치해준다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac11.png){: .align-center}

### identifier 지정

---

소스에서 셀을 지정해줄 일이 있기 때문에 Table View Cell에 myCell이라 id를 지정해주었다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac12.png){: .align-center}

### Class 파일 생성과 연결

---

Cocoa Touch Class를 사용해 상속 받을 부모 클래스를 각각 지정해주었다.

> Home Scene과 연결할 파일

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac13.png){: .align-center}

> Add Scene과 연결할 파일

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac14.png){: .align-center}

> Detail Scene과 연결할 파일

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac15.png){: .align-center}

### 소스 작성

---

#### 목록 배열 생성

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac16.png){: .align-center}

모든 곳에서 이 목록을 사용할 것이기 때문에 클래스 밖에 선언을 해준다.

#### 섹션 수 지정

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac17.png){: .align-center}

위의 `numberOfSections()`의 리턴값을 1로 바꿔 섹션이 하나 존재할 수 있게 만든다.

그리고 행의 수를 배열의 개수로 지정을 해준다.

```swift
...
return items.count // 배열의 개수
}
```

#### cellForRowAt 메소드 수정

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac18.png){: .align-center}

주석 처리된 cellForRowAt 메소드를 수정해준다.

이 메소드는 각 행의 내용을 리턴하는 역할이다.

아까 테이블 뷰 셀에 지정해 주었던 Identifier를 `withIdentifier:` 다음에 적어준다.

그리고 아래 소스를 추가해 각 셀에 배열의 내용이 표시될 수 있게끔 만들어준다.

```swift
cell.textLabel?.text = items[indexPath.row] // items[행 번호] ex. items[0], items[1], ...
// textLabel이 옵셔널값이기 때문에 옵셔널 체이닝을 이용한다.
cell.imageView?.image = UIImage(named: itemsImageFile[indexPath.row])
```

##### dequeueReusableCell

테이블 뷰는 셀 하나 하나를 다 만드는 게 아니라 재사용 할 수 있는 건 재사용을 하여 셀을 만든다.

조금 더 자세히 말하자면 재사용 큐에 재사용 가능한 것이 있다면 하고 없다면 셀을 새로 생성하는 것이다.

그래서 dequeueReusableCell 메소드로 재사용이 가능한 셀이 있는지 확인한다.

### 결과

---

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/pracresult.gif){: .align-center}

아직 구현이 안된 화면이 많지만 테이블 뷰와 네비게이션 컨트롤러는 정상적으로 작동한다.

**Notice:** 이 게시물은 [Smile Han](https://www.youtube.com/watch?v=OuP4kH2KVd4&list=PL01dF8Z1_htAwkwI5hwt4Enc_HPP7EYia&index=2)님의 유튜브를 참고하였습니다.
{: .notice--info}