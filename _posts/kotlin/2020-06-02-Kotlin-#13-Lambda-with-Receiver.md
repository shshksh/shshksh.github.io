---
title: "Kotlin for Java Developers Week 4 - Lambda with Receiver"
excerpt: "Kotlin lambda with Receiver"
classes: wide
categories: 
  - kotlin
tag:
  - kotlin
read_time: false
last_modified_at: 2020-06-02T21:30:00+09:00
toc: true
---

> 출처 : [coursera - Kotlin for Java Developers](https://www.coursera.org/learn/kotlin-for-java-developers/lecture/P62Ej/from-java-to-kotlin)
>
> 이 글은 개인 공부용으로 작성되었으며 coursera 의  Kotlin for Java Developers 강의를 의역하였습니다.



## Lambda with Receiver

코틀린에서 **Lambda with Receiver** 는 확장 함수와 람다의 결합을 말한다.

**with**

```kotlin
val sb = StringBuilder()
sb.appendln("Alphabet: ")
for (c in 'a'..'z') {
    sb.append(c)
}
sb.toString()
```

`with` 를 사용하면 람다 내에서 리시버를 명시적으로 호출하는것을 생략할 수 있다.

```kotlin
val sb = StringBuilder()
with (sb) {
    appendln("Alphabet: ")
    for (c in 'a'..'z') {
        append(c)
    }
    toString()
}
```

`with` 는 코틀린 표준 라이브러리의 함수이며, 람다를 두 번째 매개변수로 받는다. 함수 호출 시 람다가 마지막 매개변수라면 괄호 밖에서 선언할 수 있으며 람다 내부에서 `this` 를 생략하여 위와 같은 결과가 나온다.

**buildString**

문자열을 생성할 때 사용할 수 있다.

```kotlin
val s: String = buildString {
    appendln("Alphabet: ")
    for (c in 'a'..'z') {
        append(c)
    }
}
```

`buildString` 함수는 내부적으로 다음의 코드처럼 구현되어 있다.

```kotlin
inline fun buildString(builderAction: StringBuilder.() -> Unit): String {
    val stringBuilder = StringBuilder()
    stringBuilder.builderAction()
    return stringBuilder.toString()
}
```

매개변수로 `lambda with receiver` 를 가지며 매개변수의 타입이 `StringBuilder.() -> Unit` 으로 정의된것을 볼 수 있다. 매개변수로 받은 람다는 리시버에 대한 확장함수처럼 람다 내에서 리시버의 함수를 직접적으로 호출할 수 있다.



## More useful library functions

**functions**

```kotlin
inline fun <T, R> with(receiver: T, block: T.() -> R): R = receiver.block()

inline fun <T, R> T.run(block: T.() -> R): R = block()

inline fun <T, R> T.let(block: (T) -> R): R = block(this)

inline fun <T> T.apply(block: T.() -> Unit): T { block(); return this }

inline fun <T> T.also(block: (T) -> Unit): T { block(this); return this }
```

**with**

이전에 본 것 처럼 `with` 는 매개변수를 람다 내에서 리시버로 사용할 수 있다.

```kotlin
with (window) {
    width = 300
    height = 200
    isVisible = true
}
```

람다 내에서는 `window` 를 명시적으로 호출하지 않고도 멤버 함수를 직접적으로 사용할 수 있다.

**run**

`run` 은 `with` 와 비슷하게 보이지만 `nullable` 리시버와 같이 사용할 수 있는 확장 함수로 구현되어 있다.

위의 `with` 예시에서 `window` 가 `nullable` 타입이라면 람다 내에서는 항상 명시적으로 `null` 체크가 이루어 져야한다. 하지만 `run` 은 리시버에 대해 `safe call` 이 가능하기 때문에 이러한 문제를 피할 수 있다.

```kotlin
val windowOrNull = windowById["main"]
windowOrNull?.run {
    width = 300
    height = 200
    isVisible = true
}
```

**apply**

`apply` 의 경우 반환값에 있어서 `with`, `run` 과 차이가 있다.

`with` 와 `run` 은 함수의 반환값으로 람다의 수행 결과가 반환되는 반면에 `apply` 는 람다를 수행하고, 리시버를 반환된다.

```kotlin
val mainWindow = 
    windowById["main"]?.apply {
        width = 300
        height = 200
        isVisible = true
    } ?: return
```

위의 예시에서는 "main" 에 대한 `window` 가 `null` 이 아닐때만 `window` 의 속성을 변경한 객체가 `mainWindow` 에 할당된다.

**also**

`also` 함수 또한 `apply` 와 비슷한 형태를 가지고 있지만 매개변수로 리시버가 있는 람다가 아니라 일반 람다를 받는다. 리시버가 있는 람다는 람다 내에서 리시버의 호출을 생략하고 멤버 함수를 호출할 수 있기 때문에 유용하다. 

하지만 리시버를 다른 함수의 인자로 넘겨야 하는 경우라면 `also` 함수는 일반 람다를 매개변수로 사용하기 때문에 더욱 유용할 수 있다.

```kotlin
windowById["main"]?.apply {
    width = 300
    height = 200
    isVisible = true
}?.also {
    showWindow(it)
}
```

**let**

`let` 함수의 형태를 보면 매개변수로 일반 람다를 받으며 리시버를 람다의 매개변수로 넘겨주고 람다의 결과를 반환한다.

![kotlin_lambda_with_receiver](/assets/images/kotlin_lambda_with_receiver.png)

