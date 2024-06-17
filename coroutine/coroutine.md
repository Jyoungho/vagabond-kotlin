
## 부록. coroutines

코루틴은 컴퓨터 프로그램 구성 요소 중 하나로 비선점형 멀티태스킹을 수행하는 일반화한 서브루틴이다. 코루틴은 실행을 임시 중단하고 재개할 수 있는 여러 진입 지점을 허용한다.
비선점형이란 멀티태스킹의 각 자업을 수행하는 참여자들의 실행을 운영체제가 강제로 일시 중단시키고 다른 참여자를 실행하게 만들 수 없다는 뜻이다. 따라서 각 참여자들이 서로 자발적으로 협력해야만 비선점형 멀티태스킹이 제대로 동작할 수 있다.


### launch, async
kotlinx.coroutines 라이브러리를 통하여 코루틴을 사용할 수 있고 대표적인 코루틴은 launch, async 가 존재한다.
이 둘의 차이점은 launch 는 반환값이 없는 비동기 처리이고, async 는 반환값이 있는 비동기 처리 방식이다.
``` kotlin
val job = launch {
    // 비동기 작업 수행
}
job.join() // 작업이 완료될 때까지 기다림
```

``` kotlin
val deferred = async {
    // 비동기 작업 수행 후 결과 반환
    "Result"
}
val result = deferred.await() // 결과를 기다리고 반환받음
```

### suspend
서스팬딩 함수를 정의할 때 사용됩니다. suspend 함수는 직접호출 할 수 없고, 반드시 코루틴이나 다른 suspend 함수에서 호출되어야 사용이 가능하다.

``` kotlin
import kotlinx.coroutines.*

suspend fun doSomething() {
    delay(1000L) // 1초 동안 일시 중단
    println("Task completed in doSomething")
}

fun main() = runBlocking {
    doSomething() // runBlocking 코루틴 내에서 호출
}

```


이러한 코루틴은 논블로킹 처리인 webFlux 를 만날때 폭풍적인 효과를 볼 수 있다.