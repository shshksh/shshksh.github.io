---
title: "Kotlin for Java Developers Week 5 - Sequences"
excerpt: "Kotlin Sequences"
classes: wide
categories: 
  - kotlin
tag:
  - kotlin
read_time: false
last_modified_at: 2020-06-01T23:50:00+09:00
toc: true
---

> 출처 : [coursera - Kotlin for Java Developers](https://www.coursera.org/learn/kotlin-for-java-developers/lecture/P62Ej/from-java-to-kotlin)
>
> 이 글은 개인 공부용으로 작성되었으며 coursera 의  Kotlin for Java Developers 강의를 의역하였습니다.



## Collections vs Sequences

코틀린에서 컬렉션에 대해 함수적 프로그래밍을 지원하는 모든 확장 함수들은 `inline` 으로 정의되어 있다. 여기서 한가지 알아야 할 점은 이러한 함수들이 **새로운 컬렉션을 반환**한다는 점이다.

이전에 소개한것 처럼 컬렉션의 확장함수들은 `inline` 으로 정의되어 퍼포먼스적인 오버헤드가 없다. 하지만 단지 하나의 호출시에 해당하며, 연결된 호출에서는 중간단계의 컬렉션들이 **계속 새로 생성**된다. 

코틀린에서는 `sequence` 를 사용하여 이러한 문제를 피할 수 있다.

**Sequences**

컬렉션의 함수들은 각각의 호출마다 바로바로 컬렉션을 생성하는 반면 시퀀스는 **연산을 보류**하여 중간단계의 컬렉션이 생성되는것을 막는다.

컬렉션은 `asSequence` 함수를 사용하여 쉽게 시퀀스로 변환할 수 있다.

```kotlin
val list = listOf(1, 2, -3)
val maxOddSquare = list
        .asSequence()
        .map { it * it }
        .filter { it % 2 == 1 }
        .max()
```



## More about Sequences

컬렉션을 통한 함수 호출은 각각의 호출마다 결과로 새로운 컬렉션을 반환한다. 따라서 중간단계의 모든 연산 과정을 확인할 수 있다.

하지만 시퀀스를 사용할때는 결과값이 필요한 상황이 오기 전까지 아무런 동작을 하지 않는다. 시퀀스를 사용한 `Lazy evaluation` 은 최종 처리 결과가 호출됬을때 부터 연산을 수행하며, 연산 과정에서 원하는 결과를 얻었다면 결과를 반환하고 수행을 중단한다. 따라서 불필요한 컬렉션이 생성되는것을 방지한다.

컬렉션과 시퀀스의 동작 과정은 `eager, lazy evaluation` 뿐만 아니라 `Horizontal, Vertical evaluation` 으로도 비유된다. 컬렉션의 경우 순차적으로 모든 컬렉션을 새로 생성하며 마지막에 결과를 찾지만, 시퀀스는 컬렉션의 첫 번째 요소부터 연산을 시작하여 원하는 결과를 찾으면 수행을 중단한다.

```kotlin
fun m(i: Int): Int { 
  print("m$i ")
  return i 
}

fun f(i: Int): Boolean { 
  print("f$i ")
  return i % 2 == 0
}

val list = listOf(1, 2, 3, 4) 
list.map(::m).filter(::f)  //m1 m2 m3 m4 f1 f2 f3 f4
list.asSequence().map(::m).filter(::f).toList() //m1 f1 m2 f2 m3 f3 m4 f4

list.asSequence().map(::m).filter(::f) // nothing printed
```

예시의 마지막 라인의 경우 결과에 대해 아무런 요청이 없기 때문에 시퀀스는 어떤 동작도 하지 않는다. 시퀀스의 중간 단계 함수들은 또다른 시퀀스를 반환하지만, 최종 단계 함수들은 모든것을 반환한다. 

**order**

시퀀스를 사용할 때는 호출하는 함수의 순서도 중요하게 작용한다. 시퀀스에서 `map` 이후 `filter` 를 수행하면, 먼저 `map` 이 먼저 시퀀스의 모든 요소에 대해 수행되지만, 순서를 변경하면 `filter` 가 먼저 수행되어 `map` 의 적용 대상을 줄일 수 있다.

```kotlin
fun m(i: Int): Int { 
  print("m$i ")
  return i 
}

fun f(i: Int): Boolean { 
  print("f$i ")
  return i % 2 == 0
}

val list = listOf(1, 2, 3, 4) 
list.asSequence().map(::m).filter(::f).toList()
//m1 f1 m2 f2 m3 f3 m4 f4
list.asSequence().filter(::f).map(::m).toList() // f1 f2 m2 f3 f4 m4
```



## Creating Sequences

시퀀스는 코틀린 표준 라이브러리에 정의된 인터페이스로 루핑을 위한 `iterator` 함수만을 가진다.

```kotlin
interface Sequence<out T> {
    operator fun iterator(): Iterator<T>
}
```

시퀀스의 함수들은 컬렉션의 함수들과 비슷한 함수명을 가지고 있기 때문에 컬렉션 체인의 앞에 시퀀스 변환 함수를 추가하는것 만으로 쉽게 변환할 수 있다.

시퀀스의 중간 함수들은 연산이 즉시 이루어지지 않고 연기되기 때문에 `inline` 으로 정의되어있지 않다. 최종 함수의 호출 이후 늦은 연산을 위해 각각의 단계가 저장되어 있어야 한다.

시퀀스의 최종 함수들은 `inline` 으로 선언되어 있다.

**generateSequence**

`generateSequence` 함수를 사용하여 시퀀스를 생성할 수도 있다. 이 함수는 매개변수로 받는 람다의 리턴이 `null` 일 때 까지의 요소로 시퀀스를 생성한다.

```kotlin
val input = generateSequence {
    readLine().takeIf { it != "exit" }
}
println(input.toList())
```

시퀀스 생성 함수의 매개변수로 초기값과 직전값에 기반한 요소를 결정하는 람다를 사용하여 초기값에 기반한 시퀀스를 생성할 수도 있다.

```kotlin
val numbers = generateSequece(0) { it + 1 }
numbers.take(5).toList()
```

위의 예시에서 `generateSequence` 함수의 반환값은 끝이 없는 무한 수열을 의미하지만, 시퀀스의 `lazy evaluation` 으로 인해 처리 결과를 요청받기 전까지는 동작하지 않는다.

**yield**

`yield` 를 사용하면 사용자가 원하는 시퀀스를 커스터마이즈 할 수 있다.

```kotlin
val numbers = sequence {
    var x = 0
    while (true) {
        yield(x++)
    }
}
numbers.take(5).toList() // 0 1 2 3 4 
```