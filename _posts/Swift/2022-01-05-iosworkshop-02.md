---
title: "[Swift 문법 정리] 옵셔널(optional)"
date: 2022-01-05
categories:
  - Swift
tags:
  - swift
  - optional
  - 옵셔널
  - 옵셔널 바인딩
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

### 옵셔널(Optional)이란?

---

옵셔널 타입에 관해 설명하기 앞서 10과 Optional(10)의 차이가 무엇일까. 

10은 그 값 자체로 연산이 가능하지만 Optional(10)은 옵셔널을 풀어주어야 연산이 가능하다.

그렇다면 옵셔널을 쓰는 이유는 무엇일까.

옵셔널은 일반적인 값 이외에도 nil(값이 없음)을 저장할 수 있기 때문이다.

변수 또는 상수에 아무런 값이 할당되지 않는 경우에 오류가 발생할 수 있기 때문에 옵셔널이라는 데이터 타입으로 감싸주어 안전하게 반환을 하게 된다.

```swift
// 괄호 안의 문자열을 Int형으로 변환해주는 Int()
// 자료형의 경우 구조체로 되어있어 괄호를 열게 되면 initializer가 호출된다.
print(Int("10")) // Optional(10)
print(Int("H")) // nil
```

위의 소스를 실행해보면 경고 메세지가 뜨긴 하지만 값이 출력되긴 한다.

10은 정수형으로 변환이 가능하기 때문에 문제가 없었지만, 문자 'H'를 정수형으로 변환하려고 하면 문제가 된다.

따라서 Int형 initializer가 리턴값을 옵셔널로 감싸서 숫자가 아닌 문자를 받았을 때 안전하게 nil을 반환하도록 한 것이다.

옵셔널 타입은 nil 값을 저장할 수 있는 유일한 데이터 타입으로 정말 많은 곳에 사용되기 때문에 꼭 알아두어야 한다.

> nil이나 Optional에 대해 자세하게 알고 싶다면 [docs.swift.org](https://docs.swift.org/swift-book/LanguageGuide/TheBasics.html#ID330) 사이트를 참고하면 된다.

### 옵셔널 변수 선언

---

옵셔널 변수를 선언하고 싶다면 자료형 뒤에 물음표(?)나 느낌표(!)를 써주면 된다.

```swift
var optionalInt : Int? // Optional Int형 
let optionalDouble : Double? // Optional Double형 
let optionalString : String? // Optinal String형 

print(optionalInt) // nil, 초기값을 안주었을 경우 nil이 초기값이 된다.
optionalInt = 10
print(optionalInt) // Optional(10)

optionalInt = optionalInt + 2 // 오류, 옵셔널값과 정수값은 연산이 불가하다.
var x : Int = optionalInt // 오류, 옵셔널 변수의 값은 정수형 변수에 저장이 불가하다.
```

옵셔널 변수를 선언하게 되면 정수형으로 10을 저장하고 싶어도 Optional(10)으로 저장된다.

느낌표(!)를 사용하는 옵셔널은 무엇일까.

?와 !의 차이는 아래와 같다.

`var x : Int?` | `var x : Int!`
--- | ---
옵셔널 값이 nil일 경우가 있을 때 사용한다. | 옵셔널이 항상 유효한 값을 가지는 경우에 사용한다.
[강제 언래핑](#옵셔널-강제-언래핑forced-unwrapping-1) 또는 [옵셔널 바인딩](#옵셔널-강제-언래핑-2-옵셔널-바인딩optional-binding)을 해주어야 값에 접근이 가능하다. | 강제 언래핑 또는 옵셔널 바인딩 없이 값에 접근이 가능하다.

Int!의 경우 항상 유효한 값을 가진다 확신하므로 암묵적인 언래핑(implicitly unwrapped)이 되도록 선언해주는 것이다.

그렇다고 해도 옵셔널이기 때문에 nil값도 저장이 가능하다.

보통 클래스의 아웃렛 변수 초기화(자동 생성되는 코드)에서 많이 사용된다.

```swift
let a : Int! = 10 // Optional(10)
let b : Int = a // 옵셔널로 사용되지 않으면 자동으로 언래핑 된다.
let c : Int = a! // 따로 언래핑을 해줄 수도 있다.
let d = a // Optional(10), 옵셔널로 사용될 수 있을 땐 옵셔널로 사용된다. 
let e = a + 10 // 자동으로 언래핑 된다.

print(a, b, c, d, e)
// Optional(10) 10 10 Optional(10) 20
print(type(of:a), type(of:b), type(of:c), type(of:d), type(of:e))
// Optional<Int> Int Int Optional<Int> Int
```

### 옵셔널 값을 언래핑 하는 법

---

#### 옵셔널 강제 언래핑(forced unwrapping) 1

옵셔널로 감싸진(wrapped) 변수 또는 상수를 풀고 싶다면 어떻게 해야 할까.

가장 간단한 방법으로는 변수나 상수 뒤에 느낌표(!)를 붙이는 것이다.

하지만 이 방법의 경우 nil을 언래핑 하려고 했을 때 오류가 발생하기 때문에 값이 항상 존재할 것이라 확신하는 경우에만 사용해야 한다.

따라서 if문을 사용해 nil이 아닐때만 옵셔널을 풀게끔 해주어 오류를 방지할 수 있다.

```swift
var optionalInt : Int? = 10

print(optionalInt) // Optional(10)
print(optionalInt!) // 10, forced unwrapping
```

```swift
var x : Int?
x = 10
if x != nil {
    print(x!) // x의 값이 존재하면 옵셔널을 풀어 그 값을 출력한다.
} else {
    print("값이 없습니다.") // x의 값이 없다면 "값이 없습니다."를 출력한다.
}
// 실행 결과는 10
```

<div class="notice--danger" markdown="1">
**Caution:** `if x!=nil` 또는 `if x!= nil`이라 썼을 경우 `error: 'nil' cannot be assigned to type 'Int'`라는 에러가 나게 된다. 간격에 주의하도록 하자.
</div>

#### 옵셔널 강제 언래핑 2: 옵셔널 바인딩(optional binding)

옵셔널 바인딩이란 강제 언래핑하는 또 다른 방법으로 옵셔널에 할당된 값을 임시 변수 또는 상수에 할당한다.

많이 쓰이는 방법이며 형식은 아래와 같다.

```swift
if let [상수명] = [옵셔널변수명] {
    // 옵셔널 변수에 값이 있다면 언래핑하여 일반 상수에 대입 후 if문을 실행한다.
    // 값이 없을 경우(nil) if문의 조건이 거짓이 되어 if문을 실행하지 않는다.
}

if var [변수명] = [옵셔널변수명] {
    // if let과 같은 방식이다.
}
```

위와 같이 옵셔널 변수가 일반 상수나 변수에 대입이 되면서 if문 안에서 일반 상수나 변수처럼 사용이 가능하게 된다.

```swift
var x : Int? = 10

if let a = x {
    print(a)
} else {
    print("값이 없습니다.")
}
// 실행 결과는 10
```

옵셔널 바인딩을 이용하여 x의 값을 출력하는 소스이다.

이처럼 따로 느낌표(!)를 써주지 않아도 Optional(10)이 아닌 10이 출력된다.

#### 여러 옵셔널 값을 한 번에 언래핑

옵셔널 값이 여러개라면 하나하나 if문에 넣어주어야 할까?

그럴 때는 콤마(,)를 이용하면 한 번에 언래핑이 가능하다.

```swift
let x : Int? = 5
let y : Double? = 1.0
let z : String? = "Hi"

if let xx = x, let yy = y, let zz = z { // 모든 상수가 nil이 아니어야 한다.
// 콤마(,)는 &&를 의미한다.
    print(xx, yy, zz)
    print(x, y, z)
} else {
    print("nil")
}
// 5 1.0 Hi
// Optional(5) Optional(1.0) Optional("Hi")
```

주의해야 할 점은 하나라도 nil일 경우 if문 실행이 안된다는 점이다.


**Notice:** 이 게시물은 Smile Han님의 유튜브를 참고하였습니다.
[https://www.youtube.com/channel/UCM8wseo6DkA-D7yGlCrcrwA](https://www.youtube.com/channel/UCM8wseo6DkA-D7yGlCrcrwA)
{: .notice--info}