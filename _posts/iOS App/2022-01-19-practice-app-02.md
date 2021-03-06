---
permalink: /autolayout/iosapp-03/
classes: wide
title: "[앱 개발] 간단한 테이블 뷰 앱 만들어보기 2"
date: 2022-01-19
categories:
  - iOS App
tags:
  - swift
  - iOS
  - table view
  - navigation controller
excerpt: "디테일 페이지와 목록 추가 페이지 구현하기"
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

### 목록 삭제 기능 추가

---

#### 스와이프로 삭제

목록을 옆으로 스와이프하면 삭제할 수 있는 버튼이 나오게 기능을 추가할 것이다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac20.png){: .align-center}

TableViewController.swift 파일에 주석처리 되어있는 위 메소드를 사용할 것이다.

```swift
override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCellEditingStyle, forRowAt indexPath: IndexPath) {
    if editingStyle == .delete {
        // 추가
        items.remove(at: indexPath.row) // 해당 행의 제목 배열 삭제
        itemsImageFile.remove(at: indexPath.row) // 이미지 파일도 삭제

        tableView.deleteRows(at: [indexPath], with: .fade)
        //                                    fade animation을 사용한다.
    } else if editingStyle == .insert {

    }
}
```

> 실행 화면

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac21.png){: .align-center}

삭제 버튼이 영어로 나오는 걸 확인할 수 있다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac22.png){: .align-center}

만약 버튼이 한글로 나오게끔 하고 싶다면 title을 쳤을 때 나오는 위의 메소드를 사용해주면 된다.

```swift
// 메소드 앞에 override를 추가해주어야 한다.
override func tableView(_ tableView: UITableView, titleForDeleteConfirmationButtonForRowAt indexPath: IndexPath) -> String? {
    return "삭제" // 버튼의 텍스트가 "삭제"로 나오게 된다.
}
```

> 수정 후 실행 화면

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac23.png){: .align-center}

#### Edit 버튼으로 삭제

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac24.png){: .align-center}

viewDidLoad() 함수에 주석처리 되어있는 위의 소스를 사용할 것이다.

하지만 `rightBarButtonItem`에는 이미 add 버튼이 추가되어있다.

그렇기 때문에 `leftBarButtonItem`으로 바꿔준다.

> 실행 화면

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/delete.gif){: .align-center}

잘 적용된 걸 볼 수 있다.

### 목록 순서 변경 기능 추가

---

추가된 edit 버튼을 누르면 목록의 순서도 변경이 가능하게 만들어 줄 것이다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac25.png){: .align-center}

주석 처리 되어있는 위의 메소드를 사용한다.

```swift
//                                                      옮기고 싶은 IndexPath         옮길 위치
override func tableView(_ tableView: UITableView, moveRowAt fromIndexPath: IndexPath, to: IndexPath) {
    // 옮길 행의 제목과 이미지 파일을 저장한다.
    let itemToMove = item[fromIndexPath.row]
    let itemImageToMove = itemsImageFile[fromIndexPath.row]
    // 저장을 했으니 옮길 행의 제목과 이미지 파일을 지운다.
    items.remove(at: fromIndexPath.row)
    itemsImageFile.remove(at: fromIndexPath.row)
    // 옮기고 싶은 행에 저장한 제목과 이미지 파일을 삽입한다.
    items.insert(itemToMove, at: to.row)
    itemsImageFile.insert(itemImageToMove, at: to.row)
}
```

위와 같이 적어주면 된다.

복잡해보이지만 결국 이동할 데이터 저장, 원래 저장되어 있던 데이터 삭제, 이동할 곳에 데이터 삽입 이 과정을 적어준 것이다.

> 실행 화면

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/move.gif){: .align-center}

### 목록 추가 기능 추가

---

AddViewController.swift 파일에 연결해 놓았던 btnAddItem 메소드에 아래 소스를 추가해준다.

```swift
@IBOutlet weak var addItem: UITextField!
@IBAction func btnAddItem(_ sender: UIButton) {
    items.append(addItem.text!)
    // 텍스트 필드에 입력한 내용을 배열에 추가한다.
    itemsImageFile.append("sun.png")
    // 추가하는 목록의 이미지는 "sun.png"로 고정해주었다.
    addItem.text=""
    // 추가한 뒤 텍스트 필드를 비워준다.
    _ = navigationController?.popViewController(animated: true)
    // navigationController - 가장 가까운 조상 컨트롤러이다.
    // show 옵션으로 세그웨이를 연결을 해주었으므로 popViewController를 통해 Table View로 돌아가게 된다.
    // _를 사용한 이유는 popViewController의 리턴값이 있는데 이를 사용하지 않겠다는 의미이다.
}
```

추가해주고 실행을 해도 테이블 뷰에 목록이 그대로이다. 왜일까?

#### viewDidLoad() vs. viewWillAppear()

- viewDidLoad() : 뷰가 로드될 때 한 번만 호출 되는 메소드

- viewWillAppear() : 뷰가 노출될 때마다 매번 호출 되는 메소드

테이블 뷰에 목록을 추가하고 싶다면 TableViewController.swift 파일 내 `viewWillAppear()` 메소드에 아래 소스를 추가해주면 된다.

```swift
override func viewWillAppear(_ animated: Bool) {
    tvListView.reloadData()
    // 연결해둔 테이블뷰를 리로드해준다.
}
```

> 실행 화면

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/add.gif){: .align-center}

정상적으로 추가가 된다.

### 디테일 페이지 추가

---

DetailViewController.swift 파일에 아래 소스를 추가해준다.

```swift
var receiveItem = ""
// 넘겨줄 문자열 변수를 선언해준다.
```

추가로 viewDidLoad() 메소드에 아래 소스를 추가해준다.

```swift
lblItem.text = receiveItem
```

`receiveItem`을 받을 메소드도 추가해준다.

```swift
func receiveItem(_ item: String) {
    receiveItem = item
    // receiveItem에 String형 item을 받아 저장해주는 메소드
}
```

#### 세그웨이가 실행되기 전에 호출되는 메소드: prepare

TableViewController.swift 파일 제일 아래쪽에 주석 처리 되어있다.

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/prac27.png){: .align-center}

```swift
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
//                    호출한 세그웨이                세그웨이를 실행하는 트리거(목적지 작성)
    if segue.identifier == "detailSegue" {
        // "detailSegue" 세그웨이가일 때 실행하도록 한다.
        let cell = sender as! UITableViewCell
        // 셀을 누르면 실행되게 다운캐스팅하여 cell 상수에 저장한다.
        let indexPath = self.tvListView.indexPath(for: cell)
        // 현재 셀의 indexPath 정보를 indexPath에 저장한다.
        let detailView = segue.destination as! DetailViewController
        // destination은 UIViewController형이고 DetailViewController의 부모는 UIViewController이다.
        // 부모를 자식으로 다운캐스팅을 해준다.
        detailView.receiveItem(items[(indexPath?.row)!])
        // 아까 추가해준 메소드 receiveItem에 현재 행의 제목을 넘겨준다.
    }
}
```

위의 소스를 추가해주면 세그웨이가 실행되기 전에 현재 행의 제목이 receiveItem으로 넘어가서 텍스트 레이블에 제목이 출력되게끔 한다.

> 실행 화면

![image]({{ site.url }}{{ site.baseurl }}/assets/images/iosapp/detail.gif){: .align-center}

**Notice:** 이 게시물은 [Smile Han](https://www.youtube.com/watch?v=OuP4kH2KVd4&list=PL01dF8Z1_htAwkwI5hwt4Enc_HPP7EYia&index=2)님의 유튜브를 참고하였습니다.
{: .notice--info}