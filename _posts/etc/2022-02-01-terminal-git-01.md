---
title: "[Git & GitHub] Git 터미널 명령어 1"
date: 2022-02-01
categories:
  - Git
tags:
  - git
exerpt: 간단한 명령어 정리 1
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

### status

---

```shell
$ git status
```

현재 브랜치와 변경 사항 등을 보여준다.

아래는 `git status`를 했을 때 표시되는 내용이다.

> #### 브랜치

```shell
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
```

현재 master branch라는 것을 알려준다.

> #### 변경 사항

```shell
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   _posts/2022-02-01-git.md
```

`new file:` | 새로 추가된 파일
`modified:` | 변경된 파일
`deleted:` | 삭제된 파일

이렇게 세 종류 정도가 자주 보게 될 것 같다.

> #### untracked

```shell
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        _posts/test.md
```

git이 추적하지 않는 파일이라고 한다.

### branch

---

```shell
$ git branch
```

브랜치 목록을 확인할 수 있고, 앞에 `*`가 붙어있는 게 현재 브랜치이다.

뒤에 여러 옵션을 붙여 브랜치 생성, 변경, 삭제가 가능하다.

#### 브랜치 생성

```shell
$ git branch [(생성할) 브랜치 이름]
```

`branch` 뒤에 적어준 이름으로 된 브랜치가 생성된다.

#### 브랜치 변경

```shell
$ git checkout [(기존) 브랜치 이름]
```

앞으로 `branch` 뒤에 적어준 브랜치를 사용하게 된다.

```shell
overtae@TESTOP-TEST MINGW64 /c/github/terminal (master)
```

바뀐 브랜치는 위와 같이 괄호 안에서 확인할 수 있다.

#### 브랜치 병합

```shell
$ git merge [(병합할) 브랜치 이름]
```

만약 같은 내용의 파일이 중복되어 존재한다면 conflict가 일어나므로 주의해주어야 한다.

conflict의 경우 `git status`로 확인이 가능하므로 확인 후 merge를 해주도록 하자.

병합의 경우 `rebase`를 이용하는 방법도 있다.

#### 브랜치 삭제

```shell
$ git branch -d [(삭제할) 브랜치 이름]
```

### add

```shell
$ git add [파일 경로]
```

`add` 뒤에 적어준 파일을 staging area에 추가한다.

<details>
<summary>Staging Area?</summary>
<div markdown="1">

Git은 3가지 영역으로 관리된다.

- Working Directory

  현재 작업하고 있는 영역(디렉토리)이다.

- Staging Area

  커밋하기 전에 `add`해준 파일들이 있는 영역이다.

- Repository

  Staging Area의 파일들을 커밋해준다면 git 저장소에 올라가게 된다.

즉, `add`는 Working Directory의 파일을 Staging Area로 옭겨준다.

</div>
</details>

만약 추가할 파일이 많을 경우 `.`를 사용하면 된다.

```shell
$ git add .
```

### commit

---

```shell
$ git commit -m "commit message"
```

Staging Area의 파일들을 Repository로 옮겨주는 명령어이다.

큰 따옴표 `" "` 안에 커밋 메세지를 적어주면 된다.

#### commit message editor

```shell
$ git commit
```

만약 커밋 메세지의 길이가 길 경우 에디터를 이용해 적어줄 수 있다.

메세지 입력 후 esc키 + `:wq`를 입력하면 종료할 수 있다.

#### add 없이 바로 commit

```shell
$ git commit -a -m "commit message"
```

따로 `add`를 하지 않고 바로 커밋을 할 수 있다.

### push

---

```shell
$ git push [저장소 이름] [브랜치 이름]
```

commit을 통해 Repository로 파일을 옮겼다면 push로 원격 Repository에 올려주는 것이다.

> #### 예시

```shell
$ git push origin master
```

위와 같이 저장소 이름은 일반적으로 orgin이다.

origin 원격 저장소 master 브랜치에 파일을 업로드 한 것이다.

#### 저장소 이름

```shell
$ git remote
```

저장소 이름을 알려준다.

#### 저장소 이름과 브랜치 이름 생략

```shell
$ git push -u [저장소 이름] [브랜치 이름]
```

`-u` 옵션을 이용해 처음 한 번만 저장소 이름과 브랜치 이름을 적어주면 다음부터는 `git push`만으로도 파일을 업로드 할 수 있다.

**Notice:** 이 게시물은 [backlog.com](https://backlog.com/git-tutorial/kr/)사이트를 참고하였습니다.
{: .notice--info}
