
## Ch09. 제네릭스

### 제네릭 타입 파라미터
- 제네릭은 타입 파라미터를 정의내릴 수 있도록 도와줌.
- 인스턴스가 생성되는 순간, 타입 파라미터는 타입 인수로 변경됨.
- 예를 들어, Map<K, V>라고 선언한 후 Map<String, Person>와 같이 특정한 인자로 변경 가능.

```kotlin
//p.385

//string을 인자로 넘겨주기 때문에 자동으로 List<String>
val authors = listOf("Dmitry", "Svetlana")

//빈 리스트를 생성하기 때문에 List<String>라는 것을 명시적으로 표시
val readers: MutableList<String> = mutableListOf()
val readers = mutableListOf<String>()

```

- 코틀린은 반드시 제네릭 타입의 타입 인자를 정의해야함.(개발자가 정의하던, 타입추론에 의해 정의되던)

#### 제네릭 함수와 프로퍼티
- 제네릭 함수를 호출할 땐, 타입 인자를 명시적으로 표현해야 하지만, 대부분 컴파일러가 타입인자를 추론할 수 있음

```kotlin

val authors = listOf("Dmitry", "Svetlana")
val readers = mutableListOf<String>(/* ... */)
fun <T> List<T>.filter(predicate: (T) -> Boolean): List<T>

>>> readers.filter { it !in authors }
>>> readers.filter<String> { it !in authors } //이렇게 명시해주지 않아도 됨

```
- 위의 예시에서, it은 String
- 컴파일러는 제네릭 타입 T가 String이 된다는 사실을 추론함.

#### 제너릭 클래스 선언
- 코틀린은 자바와 마찬가지로 꺽쇠 기호 <>를 사용해 클래스나 인터페이스를 제네릭하게 만들 수 있음.
- 이렇게 정의내린 후에는 타입 파라미터를 클래스의 본문에서 사용가능

```kotlin
interface List<T> { //List 인터페이스에 타입 파라미터 T를 정의
    operator fun get(index: Int): T //인터페이스 안에서 T를 일반 타입처럼 사용가능 
// ...
}

```

#### 타입 파라미터 제약
- 클래스나 함수에 사용할 수 있는 타입 인자를 제한하는 기능.
- ex. 리스트에 있는 원소를 모두 더하는 함수가 있을 때, 이 함수는 List<Int>나 List<Double>에 사용될 수 있지만, List<String>에는 사용 불가능하다. 이를 표현하기 위해서 타입 파라미터는 숫자만 될 수 있다는 것을 명시해야 함.
- 어떤 타입을 제네릭 타입의 타입 파라미터에 대한 상한(upper bound)으로 지정하면 그 제네릭 타입을 인스턴화할 때 사용하는 타입 인자는 반드시 그 상한 타입이거나 그 상한 타입의 하위 타입이어야 함.
- 이때 제한을 명시하기 위해 타입 파라미터 뒤에 :를 표시.

```kotlin
fun <T: Comparable<T>> max(first: T, second: T): T {
    return if (first > second) first else second
}
>>> println(max("kotlin", "java")) //문자열은 알파벳순으로 비교 됨
kotlin

/* 비교 불가능한 값 사이에 호출하면 컴파일 에러 발생 */
>>> println(max("kotlin", 42))
ERROR: Type parameter bound for T is not satisfied:
inferred type Any is not a subtype of Comparable<Any>

```

- 위에서 보이는 T의 상한은 타입은 Comparable<T>.
- String 클래스는 Comparable<String>를 상속하기 때문에 상한에 걸리지 않는다.

- 타입 파라미터 제약이 둘 이상인 경우
    - 두 개 이상의 제약을 두어야 하는 경우도 있음.


  ```kotlin
  fun <T> ensureTrailingPeriod(seq: T)
    where T : CharSequence, T : Appendable { //타입 파라미터 제약 목록
    if (!seq.endsWith('.')) {   //CharSequance 인터페이스의 확장함수 호출
        seq.append('.') //Appendable 인터페이스의 메서드 호출
    }
  }
    >>> val helloWorld = StringBuilder("Hello World")
    >>> ensureTrailingPeriod(helloWorld)
    >>> println(helloWorld)
    Hello World.
  ```
- 타입 인자가 CharSequence와 Appendable 인터페이스를 둘 다 구현해야 함.
- data를 접근하는 연산자와 데이터를 변경하는 연산자가 모두 사용 가능한 타입이어야 한다는 것.

#### 타입 파라미터를 널이 될 수 없는 타입으로 한정
- 타입 상한을 정하지 않은 타입 파라미터는 결과적으로 Any?를 상한으로 정한 파라미터와 같음.
- 만약 타입 파라미터를 널이 될 수 없는 값으로 한정하고 싶다면, 상한을 Any로 사용.

```kotlin
class Processor<T> { //T뒤에 ?가 붙지는 않지만, value는 null이 될 수 있음
	fun process(value: T) { 
		value?.hashCode()
	}
}
```

### 런타임 시 제네릭스의 동작 : 소거된 타입 파라미터와 실체화된 타입 파라미터
- JVM의 제네릭스는 보통 타입 소거를 사용해 구현됨
- 이는 런타임 시점에 제네릭 클래스의 인스턴스에 타입 인자 정보가 들어있지 않다는 뜻.

#### 실행 시점의 제네릭 : 타입 검사와 캐스트
- 자바와 마찬가지로 코틀린 제네릭 타입의 인자 정보는 런타임에 지워짐.
- 이는 제네릭 클래스 인스턴스가 그 인스턴스를 생성할 때 쓰인 타입 인자에 대한 정보를 유지하지 않는다는 뜻.
- 예를 들어, List<String>를 생성 후 문자열을 넣어도, 런타임에는 시점에는 그 객체를 오직 List로만 볼 수 있음(어떤 타입의 원소를 저장하는지 실행시점엔 모름).
- 컴파일러가 타입 인자를 알고 올바른 타입의 값만 각 리스트에 넣도록 보장해주기 때문.

- 타입 정보 소거로 인해 생기는 제약.
    - 타입 인자는 저장되지 않기 때문에 실행시점에 타입 인자 검사 불가.
    - 다음과 같이 is를 사용해 확인 불가

```kotlin
>>> if (value is List<String>) { ... }
ERROR: Cannot check for instance of erased type
```
- 저장해야하는 타입 정보의 크기가 줄어들어서 전반적인 메모리의 사용량이 줄어듦
- 타입 인자를 명시하지않고 제네릭 타입을 사용할 수 없음 -> 스타프로젝션 사용
```kotlin
if (value is List<*>) { ... }   //map,set...등이 아니라 List 인 것을 확인하기 
```
- 타입 파라미터가 2개 이상이면 모든 타입 파라미터에 *를 포함시켜야함.
- 인자를 알 수 없는 제네릭 타입을 표현할 때 스타프로젝션을 사용함.
- as나 as? 캐스팅에도 제네릭 타입을 사용할 수 있다.

```kotlin
fun printSum(c: Collection<*>) {
val intList = c as? List<Int>
?: throw IllegalArgumentException("List is expected")
println(intList.sum())
}
>>> printSum(listOf(1, 2, 3))
6

//------------------------------------
fun printSum(c: Collection<Int>) { 
    if (c is List<Int>) { //컴파일 시점에 타입 정보가 주어지면 is 검사 수행 가능
        println(c.sum())
    }
}
>>> printSum(listOf(1, 2, 3))
6

```

#### 실체화한 타입 파라미터를 사용한 함수 선언
- 제네릭 함수의 타입 인자도 호출 시 쓰인 타입인자를 알 수 없음.
```kotlin
>>> fun <T> isA(value: Any) = value is T
Error: Cannot check for instance of erased type: T


inline fun <reified T> isA(value: Any) = value is T //이제는 이 코드가 컴파일 됨
>>> println(isA<String>("abc"))
true
>>> println(isA<String>(123))
false
```
- inline 함수를 사용한다면 이러한 제약이 생기지 않음.
- 그 이유는 인라인 함수의 타입 파라미터는 실체화되므로 실행 시점에 인라인 함수의 타입 인자를 알 수 있기 때문.
- 인라인으로 만들고 타입 파라미터를 reified로 지정하면 value 타입이 T의 인스턴스인지를 실행 시점에 검사 가능함.

#### 실체화한 타입 파라미터로 클래스 참조 대신
- java.lang.Class 타입 인자를 파라미터로 받는 API에 대한 코틀린 어댑터를 구축하는 경우 실체화한 타입 파라미터를 자주 사용.
- java.lang.Class를 자주 사용하는 API의 예 -> JDK의 ServiceLoader
-

```kotlin
import java.util.ServiceLoader

val serviceImpl = ServiceLoader.load(Service::class.java)
/* reified 타입 파라미터를 사용 */
val serviceImpl = loadService<Service>()

inline fun <reified T> loadService() { //타입 파라미터를 "reified"로 표시
    return ServiceLoader.load(T::class.java)    //T::class로 타입 파라미터의 클래스를 가져옴
}
```


#### 실체화한 타입 파라미터의 제약
- 실체화한 타입 파라미터를 사용할 수 있는 경우
    - 타입 검사와 캐스팅 (is,!is,as,as?)
    - 10장에서 설명할 코틀린 리플렉션 API(::class)
    - 코틀린 타입에 대응하는 java.lang.Class 얻기 (::class.java)
    - 다른 함수를 호출할 때 타입 인자로 사용

- 실체화한 타입 파라미터 사용 불가 경우
    - 타입 파라미터 클래스의 인스턴스 생성하기
    - 타입 파라미터 클래스의 동반 객체 메소드 호출하기
    - 실체화한 타입 파라미터를 요구하는 함수를 호출하면서 실체화하지 않은 타입 파라미터로 받은 타입을 타입 인자로 넘기기
    - 클래스, 프로퍼티, 인라인 함수가 아닌 함수의 타입 파라미터를 reified로 지정하기

### 변성 : 제네릭과 하위 타입
- List<>String과 List<>Any와 같이 기저 타입이 같고 타입 인자가 다른 여러 타입이 서로 어떤 관계가 있는지를 설명하는 개념.

#### 변성이 있는 이유 : 인자를 함수에 넘기기
- 어떤 함수가 리스트에 원소를 추가하거나 변경한다면 타입 불일치가 발생할 수 있음
- List<>Any 대신 List<>String을 넘길 수 없다. 하지만 이러한 경우가 아니라면 괜찮음.

#### 클래스, 타입, 하위 타입
- 어떤 타입 A의 값이 필요한 모든 장소에 어떤 타입 B의 값을 넣어도 아무 문제가 없다면 B는 A의 하위타입
- 반대의 경우는 상위타입.
- List<>String타입은 List<>Any의 subtype?
- 이때 인스턴스 타입 사이의 subtype 관계가 성립하지 않으면 그 제네릭 타입을 무공변이라고 함.
- 만약 A가 B의 하위타입이라면, List<>A는 List<>B의 하위타입.
    - 이런 클래스나 인터페이스를 공변적이라고 함.

#### 공변성 : 하위 타입 관계 유지
- 클래스의 타입 파라미터를 공변적으로 만들면 함수 정의에 사용한 파라미터 타입과 타입 인자의 타입이 정확히 일치하지 않더라도 그 클래스의 인스턴스를 함수 인자나 반환값으로 사용할 수 있음.
- 클래스 멤버를 선언할 때 타입 파라미터를 사용할 수 있는 지점은 모두 인(in)과 아웃(out)위치로 나뉜다. 
  - 만약 T가 함수의 반환 타입에 쓰인다면 T는 아웃 위치에 있다.(그 함수는 T 타입의 값을 생산)
  - T가 함수의 파라미터 타입에 쓰인다면 T는 인 위치에 있음


#### 반공변 : 뒤집힌 하위 타입 관계
- 반공변 클래스의 하위 타입 관계는 공변 클래스의 경우와 반대. 
- Consumer<>T를 예로 들어 설명하자면,
  - 타입 B가 타입 A의 하위 타입인 경우 Consumer<>A가 Consumer<>B의 하위 타입인 관계가 성립하면 제네릭 클래스 Consumer<>T는 타입 인자 T에 대해 반공변이다.

#### in, out 위치 
- 공변성일 때 : T를 out 위치에만 사용가능(반환 타입에서만 사용)
- 반공변성 일 때 : T를 in 위치에서만 사용 가능(파라미터 위치에만 사용)
- 무공변일때 : T를 아무데나 사용 가능

#### 스타 프로젝션 : 타입 인자 대신 * 사용
- 제네릭 타입 인자 정보가 없음을 표현하기 위해 스타프로젝션을 사용함
- 스타프로젝션의 의미
  - 첫째, MutableList<*>는 MutableList<Any?>와 같지 않다. 
    - MutableList<Any?>는 모든 타입의 원소를 담을 수 있다는 사실을 알 수 있는 리스트
    - 반면 MutableList<*>는 어떤 정해진 구체적인 타입의 원소만을 담는 리스트지만 그 원소의 타입을 정확히 모른다는 의미.
    - 타입이 MutableList<*>인 리스트를 만들 수는 없음.
    - 타입이 뭔지 모른다고해서, 그 안에 아무 원소나 담아도 된다는 뜻은 아님.
  - 타입 인자 정보가 중요하지 않을 때도 스타프로젝션을 사용 가능
    - 타입 파라미터를 시그니처에서 언급하지 않을 때
    - 타입 인자 정보가 중요하지 않을 때
