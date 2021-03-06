---
title: "[Swift 문법 정리] 연산자(Operator)"
date: 2022-01-07
categories:
  - Swift
tags:
  - swift
  - operator
  - 연산자
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

### 기본 할당 연산자 '-'

---

`값이 할당되는 변수(상수)` = `할당할 값`의 형식이다.

```swift
var x
let y = 10
y = y + 10 // y에 y+10(20) 할당
```

### 산술 연산자

---

`-` (단항) | 변수 또는 표현식의 값을 음수로 만든다.
`*` | 곱셈
`//` | 나눗셈
`+` | 덧셈
`-` | 뺄셈
`%` | 나머지

### 복합 할당 연산자

---

`x += y` | `x = x + y`와 같은 의미
`x -= y` | `x = x - y`와 같은 의미
`x *= y` | `x = x * y`와 같은 의미
`x /= y` | `x = x / y`와 같은 의미
`x %= y` | `x = x % y`와 같은 의미
`x &= y` | x와 y의 bit AND 연산 결과를 x에 할당한다.
`x |= y` | x와 y의 bit OR 연산 결과를 X에 할당한다.

### 증가 연산자, 감소 연산자

---

```swift
// x의 값을 1 증가시킨다.
x = x + 1
x += 1
// x의 값을 1 감소시킨다.
x = x - 1 
x -= 1
```

Swift3 이전에는 `x++`나 `x--`도 사용이 가능했지만 Swift3에서 없어졌다.

따라서 현재는 `+`나 `+=`을 써주어야 한다.

### 비교 연산자

---

`x > y` | x가 y보다 크면 true를 반환한다.
`x >= y` | x가 y보다 크거나 같다면 true를 반환한다.
`x < y` | x가 y보다 작다면 true를 반환한다.
`x <= y` | x가 y보다 작거나 같다면 true를 반환한다.
`x != y` | x와 y가 같지 않다면 true를 반환한다.
`x == y` | x와 y가 (값이) 같다면 true를 반환한다.
`x === y` | x와 y가 (주소가) 같다면 true를 반환한다.

### 불리언 논리 연산자

---

`!` | NOT(불리언 값 또는 표현식의 결과를 반대로 바꾼다.)
`&&` | AND
`||` | OR
`^` | XOR

### 범위 연산자

---

스위프트에는 `...`와 `>`, `<`을 사용하는 범위 연산자가 존재한다.

#### 닫힌 범위 연산자(closed range operator)

```swift
x...y
// x이상 y이하 (x와 y 둘 다 범위에 포함된다.)

10...13
// 10, 11, 12, 13
```

#### 반 열린 범위 연산자(half-open range operator)

```swift
x..<y
// x이상 y미만 (y는 범위에 포함되지 않는다.)

10..<13
// 10, 11, 12
```

#### One-Sided Ranges

```swift
x...
// x이상 (x가 범위에 포함된다.)

10...
// 10, 11, 12, 13, …

...x
// x이하 (x가 범위에 포함된다.)

...3
// 0, 1, 2, 3

..<x
// x 미만 (x는 범위에 포함되지 않는다.)

..<3
// 0, 1, 2
```

### 삼항 연산자

---

`[조건] ? [참 표현식] : [거짓 표현식]`의 형식으로 `[조건]` 부분을 계산하였을 때 결과가 참일 경우 `[참 표현식]` 부분을 실행, 거짓일 경우 `[거짓 표현식]` 부분을 실행한다.

```swift
var number = 13

print("13은 \(number%2 == 0 ? "짝수" : "홀수")이다.")
// 13은 홀수이다.
```

삼항 연산자를 이용해 숫자가 짝수인지 홀수인지 알려주는 소스이다.

만약 if문을 사용했다면

```swift
var number = 13

if number%2 == 0 {
  print("13은 짝수이다.")
} else {
  print("13은 홀수이다.")
}
```

위와 같이 소스가 길어지게 된다. 이를 통해 삼항 연산자를 사용하면 소스의 길이가 줄어든다는 것을 알 수 있다.

### Nil 합병 연산자(Nil-Coalescing Operator)

---

`[옵셔널 변수] ?? [nil일 때 할당되는 값]` 형식으로 `[옵셔널 변수]`의 값이 nil이면 `[nil일 때 할당되는 값]` 부분의 값이 할당되고 nil이 아니면 옵셔널 값을 푼 값이 할당된다.

```swift
let defaultHP = 200
var damageCount : Int? // 옵셔널 변수 선언(초기값 nil)


var currentHP = defaultHP - (damageCount ?? 0)
// damageCount의 값이 nil이므로 currentHP에는 (defaultHP - 0)의 값인 200이 할당된다.
print(currentHP) // 200

damageCount = 3
print(damageCount) // Optional(3)

currentHP = defaultHP - (damageCount ?? 0)
// damageCount의 값이 nil이 아니므로 currentHP에는 (defaultHP - damageCount!)의 값인 197이 할당된다.
print(currentHP) // 197 (Optional(197)이 아니므로 주의하자.)
```

Nil-Coalescing 연산자는 위와 같이 옵셔널을 따로 풀어주지 않아도 되기 때문에 굉장히 많이 쓰인다.

> 이 외에도 연산자에 대해 더 자세하게 알고 싶다면 [developer.apple.com](https://developer.apple.com/documentation/swift/swift_standard_library/operator_declarations/) 사이트를 참고하면 된다.

**&가 붙은 연산자?** `&*`나 `&+`처럼 연산자 앞에 `&`가 붙어있는 건 오버플로우를 고려한 연산자이다.
{: .notice--primary}

**Notice:** 이 게시물은 Smile Han님의 유튜브를 참고하였습니다.<br>
[https://www.youtube.com/channel/UCM8wseo6DkA-D7yGlCrcrwA](https://www.youtube.com/channel/UCM8wseo6DkA-D7yGlCrcrwA "Smile Han님 유튜브")
{: .notice--info}