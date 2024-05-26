# 6장. 코틀린 타입 시스템
타입 시스템(Type System)은 컴퓨터 프로그래밍에서 변수, 표현식, 함수 또는 모듈과 같은 코드 요소에 데이터 유형을 할당하는 일련의 규칙으로 구성된 논리적 체계입니다. 


## 1. 하이브리드 언어
코틀린은 주로 정적 타입 시스템을 사용하기 때문에 컴파일 타임에 타입이 결정/검사됩니다. 하지만 동적 타입도 지원하기 때문에 일부 점진적 타입 검사기능도 제공합니다. 변수가 null 값을 가질 수 있는 경우, 명시적으로 nullable 타입으로 선언해야 하며, 이 변수를 사용하기 전 null 체크를 해야 합니다.

```kotlin
fun isStr(val a: String? = "HELLO"){
    // ......
}
```

## 2. 암시적 타입 변환
코틀린은 타입 안정성을 강화하기 위해 안전하지 않은 암시적 타입 변환을 허용하지 않습니다.

```kotlin
val a: Int = 1024
val b: Byte = a.toByte()
```

## 3. Null 타입 검사, 지원

코틀린은 Null 타입에 대한 검사를 지원합니다. 여기에는 ? 연산자, 엘비스 연산자, as, !!, let, 지연 초기화 등이 있습니다.

```kotlin
val name = getName() ?: "Unknown" // ? 연산자

// as 연산자
val obj: Any = "This is a string"
val str: String = obj as String

// !! 연산자
val nullableString: String? = null
val nonNullableString: String = nullableString!!

// let 함수
val nullableValue: String? = "Hello"
nullableValue?.let {
    println(it)
}

val obj: Obj by lazy {
    // 지연 초기화
}
```
