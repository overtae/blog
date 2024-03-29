---
title: "[파이썬 플라스크] 댓글 API의 PUT, DELETE 메서드 구현하기"
date: 2022-11-20
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "Flask Instagram Clone Coding"
---

## 메서드 스펙

먼저 구현할 메서드의 스펙은 다음과 같다.

```
PUT     | posts/<id>/comments/<id>/ => 특정 게시물의 특정 댓글을 수정한다.
DELETE  | posts/<id>/comments/<id>/ => 특정 게시물의 특정 댓글을 삭제한다.
```

✅ 작성자 본인만 댓글 수정이 가능해야 한다.

✅ 작성자가 아닌 사용자가 댓글 수정 요청을 보냈다면, 403 상태 코드와 함께 에러 메시지를 반환해야 한다.

✅ 댓글 수정에 성공한다면, 200 상태 코드와 함께 수정한 댓글의 내용을 반환해야 한다.

## PUT 메서드 구현 과정

먼저 현재의 PUT 메서드는 아래와 같다.

```
@classmethod
def put(cls, post_id, comment_id):
    pass
```
{: .language-python .line-numbers}

위 메서드를 바탕으로, 댓글 수정 기능 구현 시에 고려해야 할 사항은 아래와 같을 것이다.

```
- 게시물 id가 post_id인 게시물에, 댓글 id가 comment_id인 댓글이 존재하는가?
- 존재한다면, 요청을 보낸 사용자와 댓글의 작성자가 같은가?
```

### 구현

---

다양한 방법이 있겠지만 아래와 같이 구현해보았다.

```
@classmethod
@jwt_required()
def put(cls, post_id, comment_id):
    """
    특정 게시물의 특정 댓글 수정
    """
    comment_json = request.get_json()

    # 입력 데이터 검증
    validate_result = comment_schema.validate(comment_json)
    if validate_result:
        return validate_result, 400

    # 요청을 보낸 사용자
    username = get_jwt_identity()
    author_id = UserModel.find_by_username(username).id

    post = PostModel.find_by_id(post_id)
    comment = CommentModel.find_by_id(comment_id)

    # 게시물의 존재 여부 체크
    if not post:
        return {"Error": "게시물을 찾을 수 없습니다."}, 404

    # 댓글의 존재 여부 체크
    if not comment:
        return {"Error": "댓글을 찾을 수 없습니다."}, 404

    # 댓글의 작성자와 요청을 보낸 사용자가 같고,
    # 댓글의 게시물 id와 post_id가 같다면 수정
    if comment.author_id == author_id and comment.post_id == post_id:
        comment.update_to_db(comment_json)
    else:
        return {"Error": "댓글은 작성자만 수정할 수 있습니다."}, 403

    return comment_schema.dump(comment), 200
```
{: .language-python .line-numbers}

게시물과 댓글이 존재하는지 확인한 후,

요청 받은 게시물 id와 댓글의 게시물 id가 같은지 비교하고,

요청을 보낸 사용자와 댓글의 작성자가 같은지 비교해주었다.

구현하고 보니, 해당 게시물에 해당 댓글이 없는 경우 에러 메시지가

*"댓글은 작성자만 수정할 수 있습니다."*

로 떠서.. 수정이 필요해보인다. 아마 아래와 같지 않을까 싶다.

```
if comment.post_id == post_id:
    if comment.author_id == author_id:
        comment.update_to_db(comment_json)
    else:
        return {"Error": "댓글은 작성자만 수정할 수 있습니다."}, 403
else:
    return {"Error": "댓글을 찾을 수 없습니다."}, 404
```
{: .language-python .line-numbers}

## DELETE 메서드 구현 과정

현재 DELETE 메서드부터 확인해보자.

```
@classmethod
def delete(cls, post_id, comment_id):
    pass
```
{: .language-python .line-numbers}

댓글 삭제도 수정과 비슷하게 구현하면 될 것이다.

### 구현

---

정답이 아니라는 느낌이 들긴하지만, 냅다 구현해보았다.

```
@classmethod
@jwt_required()
def delete(cls, post_id, comment_id):
    """
    특정 게시물의 특정 댓글 삭제
    """
    # 요청을 보낸 사용자
    username = get_jwt_identity()
    author_id = UserModel.find_by_username(username).id

    post = PostModel.find_by_id(post_id)
    comment = CommentModel.find_by_id(comment_id)

    # 게시물의 존재 여부 체크
    if not post:
        return {"Error": "게시물을 찾을 수 없습니다."}, 404

    # 댓글의 존재 여부 체크
    if not comment:
        return {"Error": "댓글을 찾을 수 없습니다."}, 404

    if comment.post_id == post_id:
        if comment.author_id == author_id:
            comment.delete_from_db()
            return {"message": "댓글이 성공적으로 삭제되었습니다."}, 200
        else:
            return {"Error": "댓글은 작성자만 삭제할 수 있습니다."}, 403
    else:
        return {"Error": "댓글을 찾을 수 없습니다."}, 404
```
{: .language-python .line-numbers}

사실 잘 모르겠다...ㅠ

