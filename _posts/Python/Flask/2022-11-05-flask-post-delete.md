---
title: "[파이썬 플라스크] 작성자만 게시물을 삭제할 수 있게 해보기"
date: 2022-11-05
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "Flask Instagram Clone Coding"
---

## 개요

현재까지 구현한 인스타그램 API는 로그인 한 사용자라면 누구나 게시물을 작성할 수 있고, 삭제할 수도 있다. A 유저가 B 유저의 게시물을 삭제한다면 B 유저는 매우 슬플 것이다. 따라서 게시물을 작성한 본인만 삭제가 가능하게끔 구현해보자.

흐름은 아래와 같겠다.

1. `get_jwt_identity()` 로 요청을 보낸 사용자의 username을 받아온다.
2. 받아온 username을 가진 사용자의 id를 구한다.
3. 삭제를 원하는 게시물의 유저 id를 구한다.
4. 요청을 보낸 사용자 id와 게시물 작성자의 id를 비교한다.
5. 같다면 삭제하고, 다르다면 삭제하지 못한다.

**참고** username으로 비교를 하게 될 경우, 게시물을 작성한 사용자의 유저네임을 구할 때 `post.author.username` 로 접근해야하기에 `post.author_id` 로 접근 가능한 id로 비교하기로 했다.
{: .notice--info}

### 0. DELETE 요청을 처리하는 곳

---

사용자가 DELETE 요청을 보내면 어디서 처리하는 것일까.

게시물 CRUD의 경우 `resources/post.py` 에서 진행되고, Post 클래스의 delete 메서드가 삭제 요청을 처리한다.

현재 delete 메서드는 아래와 같다.

{% highlight python linenos %}
@classmethod
@jwt_required()
def delete(cls, id):
	post = PostModel.find_by_id(id)
	if post:
		post.delete_from_db()
    return {"message": "게시물이 성공적으로 삭제되었습니다."}, 200
	return {"Error": "게시물을 찾을 수 없습니다."}, 404
{% endhighlight %}

jwt가 필요하고, 게시물이 존재만 한다면 삭제가 가능하다.

### 1. 요청을 보낸 사용자의 정보

---

먼저 요청을 보낸 사용자의 정보를 저장해주자.

아래 코드로 사용자의 id를 저장해주었다.

{% highlight python linenos %}
username = get_jwt_identity()
author_id = UserModel.find_by_username(username).id
{% endhighlight %}

### 2. 요청을 보낸 사용자가 게시물의 작성자인지 확인

---

삭제 할 게시물 작성자의 id와 요청을 보낸 사용자의 id가 같은지 비교하는 조건문을 작성하자.

이때, 기본적으로 게시물이 존재한다는 가정하에 작성하였다.

{% highlight python linenos %}
if post.author_id == username:
  post.delete_from_db()
  return {"message": "게시물이 성공적으로 삭제되었습니다."}, 200
else:
  return {"Error": "게시물은 작성자만 삭제할 수 있습니다."}, 403
{% endhighlight %}

### 3. 완성

---

결과적으로 완성된 코드는 아래와 같다. — [전체 코드는 이 곳](https://github.com/overtae/my-flask-study/tree/13-flask-instagram)

```python
@classmethod
@jwt_required()
def delete(cls, id):
  # 요청을 보낸 사용자
  username = get_jwt_identity()
  author_id = UserModel.find_by_username(username).id

  post = PostModel.find_by_id(id)
  
  # 게시물이 존재하는지 확인
  if post:
    # 게시물의 작성자와 요청을 보낸 사용자가 같은지 확인
    if post.author_id == author_id:
      post.delete_from_db()
      return {"message": "게시물이 성공적으로 삭제되었습니다."}, 200  
    else:
      return {"Error": "게시물은 작성자만 삭제할 수 있습니다."}, 403  
  return {"Error": "게시물을 찾을 수 없습니다."}, 404
```

### 테스트

---

현재 존재하는 게시물은 아래와 같다.

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/instagram/del01.png" alt="">
</figure>

태영이가 작성했고, 게시물의 id는 1이다.

태영이가 아닌 태일이가 게시물을 삭제한다면,

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/instagram/del02.png" alt="">
  <figcaption>삭제가 되지 않는다.</figcaption>
</figure>

태영이가 삭제를 한다면,

<figure class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/python/flask/instagram/del03.png" alt="">
  <figcaption>성공적으로 삭제가 되었다.</figcaption>
</figure>

**Notice:** 이 게시물은 [gdsanadev 블로그](https://gdsanadev.com/15375)를 참고하였습니다.
{: .notice--info}