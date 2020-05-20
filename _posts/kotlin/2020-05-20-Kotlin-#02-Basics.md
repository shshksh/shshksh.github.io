---
title: "Kotlin for Java Developers Week 2 - Basics"
excerpt: "Kotlin Basics"
classes: wide
categories: 
  - kotlin
tag:
  - kotlin
read_time: false
last_modified_at: 2020-05-20T20:20:00+09:00
toc: true
---

> 출처 : [coursera - Kotlin for Java Developers](https://www.coursera.org/learn/kotlin-for-java-developers/lecture/P62Ej/from-java-to-kotlin)
>
> 이 글은 개인 공부용으로 작성되었으며 coursera 의  Kotlin for Java Developers 강의를 의역하였습니다.



# Basics

**Hello, World!**

```kotlin
package example

fun main() {
    val name = "Kotlin"
    println("Hello, $name!")
}
```

위의 코드의 출력 결과는 **"Hello, Kotlin!"** 이다. 여기서의 중점은 이 코드에서는 `class` 가 없다는 점이다. main 함수는 코틀린 파일의 유일한 콘텐츠이며 `top level` 에 정의된다. 예제의 코틀린 파일은 패키지와 최상위 함수의 선언만을 포함한다. 

코틀린에서는 `static utility method` 를 정의하기 위해 자바에서 처럼 모든것을 `class` 내부에 포함시킬 필요 없이 함수를 패키지 레벨에서 선언할 수 있다. 

또한 자바에서의 main 메서드는 항상 매개변수로 `String` 배열을 받지만 대체로 이 매개변수는 사용되지 않는다. 코틀린은 1.3 이후로 main 함수의 매개변수를 생략할 수 있고 필요한 경우에만 `args` 매개변수를 정의할 수 있다.

```kotlin
fun main(args: Array<String>) { /* ... */ }
```

**if expression**

```kotlin
package example

fun main(args: Array<String>) {
    val name = if (args.size > 0) args[0] else "Kotlin"
    println("Hello, $name!")
}
```

이 코드는 `args` 배열이 비어있지 않다면 `name` 배열의 첫 번째 값을 가져와서 저장하고 배열이 비어있다면 "Kotlin" 문자열을 저장한다.