---
title: "[Swift 문법 정리] 기본 자료형(데이터 타입)과 print 함수 사용법"
date: 2022-01-04
categories:
  - Swift
tags:
  - swift
  - 자료형
  - 변수
  - 상수
  - 튜플
  - print
toc: true
toc_label: "목차"
toc_icon: bars
toc_sticky: true
---

### 자료형의 종류

---

--- | ---
[Int](#정수-데이터-타입-int) | Integer의 약자, 정수(음수, 0, 양수)
[UInt](#정수-데이터-타입-int) | Unsigned Integer의 약자, 부호가 없는 정수(0, 양수)
[Float](#부동-소수점-데이터-타입-double) | 실수(32비트, 6자리 정확도)
[Double](#부동-소수점-데이터-타입-double) | 실수(64비트, 15자리 정확도)
[Character](#문자-데이터-타입-character) | 문자
[String](#문자열-데이터-타입-string) | 문자열
[Bool](#논리-데이터-타입-bool) | 참과 거짓
Void | 값이 없음(주의: 0이 아님)

### 자료형의 크기

---

자료형의 크기를 알고 싶다면 아래 소스를 작성해보면 된다.

```swift
var x = 10
print(type(of:x)) // data type 출력, Int
let sizeOfX = MemoryLayout.size(ofValue: x) // 해당 변수의 크기를 할당
let sizeOfInt = MemoryLayout<Int>.size // 정수형의 크기를 할당
print(sizeOfX, sizeOfInt) // 8, 8 (단위: byte)
```

이를 이용해 알아본 기본 자료형들의 크기이다.

--- | ---
Int | 8bytes
UInt | 8bytes
Float | 4bytes
Double | 8bytes
Character | 16bytes
String | 16bytes
Bool | 1bytes
Void | 0bytes

### 정수 데이터 타입: Int

---

부호가 있는(signed) 정수와 부호가 없는(unsigned) 정수로 나뉘며, 해당 코드가 실행되는 플랫폼에 따라 Int8, Int16, Int32, Int64 중 맞는 크기를 사용한다.

따로 크기를 지정해줄 수 있기는 하지만 애플에서는 Int 데이터 타입을 권장하고 있다.

아래는 각 데이터 타입 크기별 최솟값과 최댓값이다.

데이터 타입 | 최솟값 | 최댓값
--- | --- | ---
Int8 | -128 | 127
Int16 | -32768 | 32767
Int32 | -2147483648 | 2147483647
Int64 | -9223372036854775808 | 9223372036854775807
UInt8 | 0 | 255
UInt16 | 0 | 65535
UInt32 | 0 | 4294967295
UInt64 | 0 | 18446744073709551615

> 자료형의 최솟값과 최댓값을 출력하는 방법

형식은 아래와 같다.

최솟값 | [자료형].min
최댓값 | [자료형].max

바로 출력을 해도 되고 따로 변수에 저장해 출력을 해도 된다.

```swift
print("최솟값 : \(Int.min), 최댓값 : \(Int.max)")
```

<div class="notice--primary" markdown="1">
**\ (Int.min)이 뭘까?** 문자열 보간(string interpolation)이라고 하며 위의 소스처럼 문자와 변수(또는 상수)의 값을 같이 출력하고 싶을 때 사용한다.

```swift
let x = 10
print("x: \(x)") // x: 10
print("x: x") // x: x, 이런 상황을 방지하기 위해 사용
```
</div>

### 부동 소수점 데이터 타입: Double

---

소수점이 있는 숫자의 데이터 타입을 의미하며 Float와 Double 타입이 있다.

자료형을 따로 명시하지 않았을 경우 Double형을 기본으로 한다.

```swift
var x = 1.0
var y : Float = 1.0
print("x : \(type(of:x))\ny : \(type(of:y))")
// x : Double
// y : Float
```

### 문자 데이터 타입: Character

---

문자, 숫자, 문장 부호, 심볼 같은 유니코드(Unicode) 문자 하나를 저장한다.

C언어의 경우 작은 따옴표(')로 문자를 할당하지만 스위프트에선 Character와 String 모두 큰 따옴표(")를 사용한다.

Character 타입의 경우 생략을 하면 String형으로 인식을 하기 때문에 생략이 불가하다.

```swift
var char1 : Character = "X"
var char2 : Character = "\u{0058}" // 유니코드 형식
var char3 = "X"
var char4 = "\u{0058}"

print(char1, char2) // X X
print(type(of:char1), type(of:char2)) // Character Character

print(type(of:char3), type(of:char4)) // String String, 자료형 생략시 String형
```

### 문자열 데이터 타입: String

단어나 문장을 구성하는 일련의 문자를 저장, 검색, 비교, 문자열 연결, 수정 등의 기능을 포함하고 있다.

문자열 보간을 사용해 변수, 상수, 표현식, 함수 호출 등의 조합으로 만들 수 있다.

```swift
var today : String = "화요일"
var month = 1
var message = "오늘은 \(month)월의 \(today)이다."

print(message)
// 오늘은 1월의 화요일이다.
```

### 논리 데이터 타입: Bool

---

참(true) 또는 거짓(false) 조건을 처리하는 데이터 타입이다.

보통 0과 1로 표현이 가능하지만 스위프트에선 에러가 난다.

```swift
var x = true
var y : Bool = 1
// error: cannot convert value of type 'Int' to specified type 'Bool'
```

### 숫자를 저장하는 방법

---

스위프트에서 값을 저장하는 방법으로 상수(let, 변경 불가), 변수(var, 변경 가능)가 있다.

애플에서는 코드의 표율성과 실행 성능을 높이기 위해 변수보다는 상수를 사용하라고 권장한다.

형식은 아래와 같다.

--- | ---
변수 | var [변수명] : [자료형] = [값]
상수 | let [상수명] : [자료형] = [값]

```swift
var x = 10 // 변수 선언
let y = 20 // 상수 선언

var z : Int // 콜론 뒤에 자료형을 적어준다.(type annotation) 이때, 자료형은 대문자로 시작한다.
z = 10
```

변수와 상수 모두 초기값 없이 선언만 먼저 해두고 나중에 값을 따로 할당할 수 있지만 상수의 경우 한 번만 할당할 수 있다.

변수명 뒤에 자료형을 적어주는 것을 타입 어노테이션(type annotation)이라 한다.

초깃값이 있을 경우 컴파일러가 자료형이 뭔지 인식(타입 추론: type inference)을 하기 때문에 자료형(: Int) 생략이 가능하다.

추가로 세미콜론(;)도 따로 적어주지 않아도 된다.

> 주의

```swift
var x= 10
// error '=' must have consistent whitespace on both sides
// '=' 양쪽에 동일한 공백이 필요하다
```

위와 같이 등호(=) 양쪽으로 다른 공백이 입력되어 있다면 에러가 나게 된다. 꼭 동일한 공백을 입력하도록 하자.

### print 함수 사용법

---

print 함수의 separator와 terminator를 이용해 출력 양식을 지정해줄 수 있다.

```swift
print(1, 2, 3) // separator의 기본값은 공백이다.
// 1 2 3
print(1, 2, 3, separator: ", ")
// 1, 2, 3
for i in 1...3 {
  print(i, terminator: " ") // terminator의 기본값은 개행(\n)으로 되어있기 때문에 안쓸경우 세 줄로 출력되게 된다.
}
// 1 2 3
```

print 함수에 대한 자세한 설명의 경우 [이곳](https://developer.apple.com/documentation/swift/1541053-print)에서 확인 가능하다.
{: .notice--primary}

### 특수 문자 표현법(escape sequence)

---

특수 문자의 경우 역슬래시를 접두어로 하여 구별한다.

\n | 개행
\r | 캐리지 리턴(carriage return)
\t | 수평 탭
\\ | 역슬래시(\)
\" | 큰 따옴표(")
\' | 작은 따옴표(')
\u{nn} | 1바이트 유니코드 스칼라
\u{nnnn} | 2바이트 유니코드 스칼라
\U{nnnnnnnn} | 4바이트 유니코드 스칼라

### 튜플(Tuple)

---

튜플이란 여러 값을 하나의 개체에 일시적으로 묶는 방법이다.

어떠한 타입도 저장이 가능하며 저장된 값들이 모두 같은 타입이 아니어도 된다.

```swift
let exTuple = (1, 2.0, "A")
let (exInt, exDouble, exString) = exTuple // 각 튜플의 값을 따로 변수나 상수에 할당
//     1      2.0        A
print(exTuple.2) // A
print(exDouble) // 2.0
```

위의 소스와 같이 튜플은 괄호를 사용한다. 

튜플의 요소들에 접근을 하는 방법 중 하나로는 인덱스 위치를 참조하는 방법이 있다.

이때 첫 번째 값은 0이다.

각 튜플의 값을 따로 변수나 상수에 할당이 가능한데 만약 선택적으로 추출하고 싶다면 아래와 같이 언더 스코어(_)를 사용하면 된다.

```swift
let exTuple = (1, 2.0, "A")
let (exInt, exDouble, _) = exTuple // swift에서 _는 무시를 의미한다.
//     1      2.0
```

추가로 각 값마다 이름을 할당할 수도 있다. 할당된 이름은 각 값을 참조하는 데 사용된다.

```swift
let exTuple = (exInt: 1, exDouble: 2.0, exString: "A")

print(exTuple.exInt) // 1
```

그렇다면 튜플의 자료형은 무엇일까?

```swift
let exTuple = (1, 2.0, exString: "A")
print(type(of:exTuple))
// (Int, Double, exString: String)
```

괄호 안에 여러 자료형이 묶여있다.


**Notice:** 이 게시물은 Smile Han님의 유튜브를 참고하였습니다.
[https://www.youtube.com/channel/UCM8wseo6DkA-D7yGlCrcrwA](https://www.youtube.com/channel/UCM8wseo6DkA-D7yGlCrcrwA)
{: .notice--info}