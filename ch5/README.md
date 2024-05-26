# 5장. 람다로 프로그래밍

람다는 실행할 수 있는 코드 조각을 뜻합니다.

```kotlin
val numbers = listOf(1, 2, 3, 4)
numbers.forEach { number -> 
    println(number) 
}
```

람다 표현식에서 로컬 변수를 사용할 때, 람다는 그 변수의 값 또는 참조를 캡쳐할 수 있습니다. 캡쳐링 방식은 변경 가능인지 불가능한지에 따라 다릅니다. 즉, 불변 값을 참조하는 것이 좋습니다.

```kotlin
val finalValue = 10  // 변경 불가능한 변수
var mutableValue = 20  // 변경 가능한 변수

val lambda1 = { println(finalValue) }
val lambda2 = { println(mutableValue) }

mutableValue = 30
lambda1()  // 출력: 10
lambda2()  // 출력: 30
```

## 멤버 변수 참조 (Member Reference)

멤버 변수 참조는 특정 클래스의 멤버(필드 또는 메서드)를 참조하는 기능입니다. :: 연산자를 사용하여 클래스의 멤버를 직접 참조할 수 있으며, 이를 통해 해당 멤버를 가리키는 함수형 인터페이스로 사용할 수 있습니다.

```kotlin
class Person(val name: String, var age: Int)

// 멤버 참조
val getName: (Person) -> String = Person::name
val getAge: (Person) -> Int = Person::age
```


## 바운드 멤버 참조(Bound Member Reference)
바운드 멤버 참조는 특정 객체의 멤버를 참조하는 것을 말합니다. 멤버 참조는 클래스의 모든 인스턴스에 적용되지만, 바운드 멤버 참조는 특정 인스턴스에 고정되어 그 인스턴스의 멤버만 참조합니다.

```kotlin
val alice = Person("Alice", 28)
val aliceAge: () -> Int = alice::age

fun main() {
    println(aliceAge())  // 출력: 28
    alice.age = 29
    println(aliceAge())  // 출력: 29
}
```

## 지연 연산(Lazy Evaluation)

지연 연산(Lazy Evaluation)은 프로그래밍에서 계산의 실행을 그 값이 실제로 필요할 때까지 미루는 기법을 말합니다. 이를 통해 성능을 향상시킬 수 있습니다.



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
