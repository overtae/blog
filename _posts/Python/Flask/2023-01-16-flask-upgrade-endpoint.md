---
title: "[파이썬 플라스크] 회원가입 API 개선해보기"
date: 2023-01-16
categories:
  - Python Flask
tags:
  - python
  - flask
excerpt: "비밀번호 검증을 통해 보안성을 높여보자"
---

## 개요

[네이버 비밀번호 관리](https://privacy.naver.com/protection_activity/security_setting?menu=protection_activity_service_naver_info)를 보면 아래와 같이 안내되어 있다.

```
영어 대/소문자, 숫자, 특수문자를 혼합하여 8자리 이상으로 설정합니다.
```

이에 맞춰 Flastagram의 비밀번호에도 검증 단계를 추가하고자 한다.

## 기존 코드

아래는 현재 작성되어있는 회원가입 코드이다.

```
def post(self):
    data = request.get_json()
    validate_result = register_schema.validate(data)
    if validate_result:
        return validate_result, 400
    else:
        if UserModel.find_by_username(data["username"]):
            return {"bad request": "중복된 사용자 이름입니다."}, 400
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

`UserModel`의 메소드로 사용자 이름과 이메일의 중복을 확인하고 있다.

따라서, `UserModel`에 비밀번호를 검증하는 메소드를 추가로 작성하고자 한다.

## 수정 코드

계획에 따라, `UserModel`에 정규식을 작성해주었다.

```
import re

@classmethod
def check_password(cls, password):
    """
    비밀번호 검증
    """
    password_regex = re.compile(
        "(?=.*[A-Z])(?=.*[a-z])(?=.*[0-9])(?=.*[@!?.])[A-Za-z0-9@!?.]{{8,}}"
    )
    return password_regex.match(password)
```
{: .language-python .line-numbers}

작성 후, 아래의 코드를 추가해주었다.

```
# 생략
elif not UserModel.check_password(data["password"]):
    return {"message": "유효하지 않은 비밀번호입니다."}, 400
```
{: .language-python .line-numbers}

