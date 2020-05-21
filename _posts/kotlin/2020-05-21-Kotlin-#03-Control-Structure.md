---
title: "Kotlin for Java Developers Week 2 - Control Structure"
excerpt: "Kotlin Control Structure"
classes: wide
categories: 
  - kotlin
tag:
  - kotlin
read_time: false
last_modified_at: 2020-05-21T20:45:00+09:00
toc: true
---

> 출처 : [coursera - Kotlin for Java Developers](https://www.coursera.org/learn/kotlin-for-java-developers/lecture/P62Ej/from-java-to-kotlin)
>
> 이 글은 개인 공부용으로 작성되었으며 coursera 의  Kotlin for Java Developers 강의를 의역하였습니다.



## Control Structure

**조건문: if & when**

이전에 소개했던것 처럼 코틀린에서 `if` 는 식이기 때문에 반환 값을 변수에 할당할 수 있다. 따라서 코틀린에서는 삼항 연산자가 없다. 필요한 경우에는 `if-else` 구문으로 표현할 수 있다.

```kotlin
val c = if (a > b) a else b
```

`when` 의 경우에는 자바의 `switch` 와 유사하다. 하지만 `when` 은 `switch` 처럼 케이스마다 일일이 `break` 를 작성할 필요가 없다. 또한 조건은 쉼표로 구분하여 하나 이상을 지정할 수도 있다.

```kotlin
fun respondToInput(input: String) = when (input) {
    "y", "yes" -> "I'm glad you agree"
    "n", "no" -> "Sorry to hear that" 
    else -> "I don't understand you"
}
```

클래스의 하위 클래스를 확인할 때도`when` 을 사용할 수 있다.

```kotlin
when (pet) {
    is Cat -> pet.meow()
    is Dog -> pet.woof()
}
```

코틀린에서는 자바의 `instanceof` 대신에 `is` 를 사용하여 자료형을 확인할 수 있다.

`when` 구문에 인자를 넘기지 않은 경우엔 조건에 `Boolean` 식을 넣어서 표현할 수 있다.

**반복문**

코틀린은 자바와 동일한 반복문을 가지고 있다. `while` 과 `do-while` 은 자바와 완전히 동일하며 `for` 문의 경우 약간 다르게 표현된다. 

```kotlin
val list = listOf("a", "b", "c")
for (s in list) {
    print(s)
}
// 리스트의 반복

val map = mapOf(1 to "one", 2 to "two", 3 to "three")
for ((key, value) in map) {
    println("$key = $value")
}
// 맵의 반복

for ((index, element) in list.withIndex()) {
    println("$index: $element")
}
// 인덱스를 포함하는 반복문
```

또한 코틀린의 반복문은 범위를 이용하여 표현할 수도 있다.

```kotlin
for (i in 1..9) {
    print(i)
}

for (i in 1 until 9) {
    print(i)
}
```

위의 경우 반복은 9를 포함하며 아래의 경우는 9를 포함하지 않는다.  역순으로 반복을 할 때나 변화 범위를 수정해야 하는 경우엔 다음과 같이 사용할 수 있다.

```kotlin
for (i in 9 downTo 1 step 2) {
    println(i)
}
```

추가적으로 코틀린은 문자열에 대한 반복문을 지원한다.

**range**

코틀린만의 특징으로 코틀린은 범위에 대한 표현을 지원한다. `in` 키워드는 반복문에서 반복 범위를 표현하는데 사용할 수 있지만 소속에 대한 표현도 가능하다.

```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
```

범위에 속하지 않음을 표현할 때는 `!in` 을 이용할 수 있다. 또한 `in` 은 `when` 구문에 대한 조건으로 사용이 가능하다.

```kotlin
fun recognize(c: Char) = when (c) {
    in '0'..'9' -> "It's a digit"
    in 'a'..'z', in 'A'..'Z' -> "It's a letter"
    else -> "I don't know"
}
```

문자열의 경우 `in` 을 사용하면 사전식으로 비교하여 범위에 속하는지를 판단한다. 커스텀 클래스의 경우 `Comparable<>` 을 구현하면 `in` 을 사용할 수 있다.

**예외**

코틀린에서 예외는 자바와 유사하지만 중요한 차이점이 있다. 코틀린은 checked exception 과 unchecked exception 을 구분하지 않는다. 또한 프로그래머는 예외를 처리하지 않을 수도 있으며 함수는 수행 과정에서 발생하는 예외를 명시할 필요가 없다.

코틀린에서 `throw` 와 `try` 는 식이라서 결과 값을 변수에 저장할 수 있다.

