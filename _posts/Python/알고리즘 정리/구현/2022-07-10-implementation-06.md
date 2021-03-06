---
title: "[파이썬 알고리즘] 게임 개발 문제 풀이"
date: 2022-07-10
categories:
  - Python Algorithm
tags:
  - python
  - 알고리즘
  - 구현
excerpt: "구현 알고리즘을 이용하여 문제를 풀어보자"
---

### ❓ 문제

---

![문제]({{ site.url }}{{ site.baseurl }}/assets/images/python/implementation/05-01.png){: .align-center}

![문제]({{ site.url }}{{ site.baseurl }}/assets/images/python/implementation/05-02.png){: .align-center}


### ✏ 나의 풀이

---

{% highlight python linenos %}

# 값 입력 받기
n, m = map(int, input().split())
a, b, d = map(int, input().split())
p = [[int(x) for x in input().split()] for _ in range(n)]

# 방향 처리 (x, y)
dx = [-1, 0, 1, 0]
dy = [0, 1, 0, -1]

p[a][b] = 1
moves = 1
turns = 0

while True:
    # 반시계 방향 회전
    d = 3 if not d else d - 1
    turns += 1

    # 임시 좌표
    x, y = a + dx[d], b + dy[d]

    if not p[x][y]:
        turns = 0
        moves += 1
        a, b = x, y
        p[a][b] = 1
    elif turns == 4:
        x, y = a - dx[d], b - dy[d]
        if not p[x][y]:
            a, b = x, y
        else:
            break
    else:
        continue

print(moves)  # 답

{% endhighlight %}

캐릭터가 이동한 횟수에 뒤로 돌아가는 것도 포함인지를 몰라서 포함이 안되게 풀이를 작성하였다.


### 💡 문제 해결 아이디어

---

방향을 설정해서 이동하는 문제 유형에서는 dx, dy라는 별도의 리스트를 만들어 방향을 정하는 것이 효과적이다.

리스트 컴프리헨션 문법을 사용하면 2차원 리스트를 초기화 할 때 효율적이다.


### ✔️ 파이썬 답안 예시

---

{% highlight python linenos %}

# N, M을 공백을 기준으로 구분하여 입력받기
n, m = map(int, input().split())

# 방문한 위치를 저장하기 위한 맵을 생성하여 0으로 초기화
d = [[0] * m for _ in range(n)]
# 현재 캐릭터의 X 좌표, Y 좌표, 방향을 입력받기
x, y, direction = map(int, input().split())
d[x][y] = 1 # 현재 좌표 방문 처리

# 전체 맵 정보를 입력받기
array = []
for i in range(n):
    array.append(list(map(int, input().split())))

# 북, 동, 남, 서 방향 정의
dx = [-1, 0, 1, 0]
dy = [0, 1, 0, -1]

# 왼쪽으로 회전
def turn_left():
		# direction이 함수 바깥에서 선언된 전역변수이기 때문에 global 키워드 사용
    global direction
    direction -= 1
    if direction == -1:
        direction = 3

# 시뮬레이션 시작
count = 1
turn_time = 0
while True:
    # 왼쪽으로 회전
    turn_left()
    nx = x + dx[direction]
    ny = y + dy[direction]
    # 회전한 이후 정면에 가보지 않은 칸이 존재하는 경우 이동
    if d[nx][ny] == 0 and array[nx][ny] == 0:
        d[nx][ny] = 1
        x = nx
        y = ny
        count += 1
        turn_time = 0
        continue
    # 회전한 이후 정면에 가보지 않은 칸이 없거나 바다인 경우
    else:
        turn_time += 1
    # 네 방향 모두 갈 수 없는 경우
    if turn_time == 4:
        nx = x - dx[direction]
        ny = y - dy[direction]
        # 뒤로 갈 수 있다면 이동하기
        if array[nx][ny] == 0:
            x = nx
            y = ny
        # 뒤가 바다로 막혀있는 경우
        else:
            break
        turn_time = 0

# 정답 출력
print(count)

{% endhighlight %}


### 💬 풀이 평가

---

나의 경우 `if-elif-else` 로 풀었지만 답안에서는 따로 확인을 했다는 점이 달랐다.

따로 확인을 해주는 이유라도 있는걸까?


**Notice:** 이 게시물은 한빛미디어의 [이것이 코딩 테스트다](https://book.naver.com/bookdb/book_detail.nhn?bid=16439154) 교재를 참고하였습니다.
{: .notice--info}