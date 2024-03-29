---
title: "[파이썬 플라스크] 비즈니스 로직 알아보기"
date: 2022-11-26
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "Flask Instagram Clone Coding"
---

## 비즈니스 로직?

비즈니스 로직은 컴퓨터 프로그램에서 **데이터를 생성/표시/저장/변경하는 부분**을 말한다. 

플라스크 인스타그램 서비스에서의 비즈니스 로직은 주로 `api/resources` 에 정의되어 있다. 이를 알아보도록 하자.

### 게시물 수정

```
@classmethod
@jwt_required()
def put(cls, id):
    """
    게시물의 전체 내용을 받아서 게시물을 수정
    없는 리소스를 수정하려고 한다면 HTTP 404 상태 코드와 에러 메시지를,
    그렇지 않은 경우라면 수정을 진행
    """
    post_json = request.get_json()
    # 입력 데이터 검증
    validate_result = post_schema.validate(post_json)
    if validate_result:
        return validate_result, 400
    username = get_jwt_identity()
    author_id = UserModel.find_by_username(username).id
    post = PostModel.find_by_id(id)
    # 게시물 존재 여부 체크
    if not post:
        return {"Error": "게시물을 찾을 수 없습니다."}, 404

    # 게시물의 저자와, 요청을 보낸 사용자가 같은지 체크
    if post.author_id == author_id:
        post.update_to_db(post_json)
    else:
        return {"Error": "게시물은 작성자만 수정할 수 있습니다."}, 403

    return post_schema.dump(post), 200
```
{: .language-python .line-numbers}

1. 입력한 데이터 검증
2. 게시물 존재 여부 체크
3. 게시물의 저자 본인인지 체크

### 유저 생성

```
def post(self):
        data = request.get_json()
				# 입력 데이터 검증
        validate_result = register_schema.validate(data)
        if validate_result:
            return validate_result, 400
        else:
						# 중복된 이름인지 체크
            if UserModel.find_by_username(data["username"]):
                return {"bad request": "중복된 사용자 이름입니다."}, 400
						# 중복된 이메일인지 체크
            elif UserModel.find_by_email(data["email"]):
                return {"message": "중복된 이메일입니다."}, 400
            else:
                password = generate_password_hash(data["password"])
                user = register_schema.load(
                    {
                        "username": data["username"],
                        "email": data["email"],
                        "password": password,
                        "password_confirm": password,
                    }
                )

            user.save_to_db()
            return {"success": f"{user.username} 님, 가입을 환영합니다!"}, 201
```
{: .language-python .line-numbers}

1. 입력 데이터 검증
2. 중복된 이름인지 체크
3. 중복된 이메일인지 체크