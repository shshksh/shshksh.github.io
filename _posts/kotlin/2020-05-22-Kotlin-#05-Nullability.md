---
title: "Kotlin for Java Developers Week 3 - Nullability"
excerpt: "Kotlin Nullability"
classes: wide
categories: 
  - kotlin
tag:
  - kotlin
read_time: false
last_modified_at: 2020-05-22T23:20:00+09:00
toc: true
---

> 출처 : [coursera - Kotlin for Java Developers](https://www.coursera.org/learn/kotlin-for-java-developers/lecture/P62Ej/from-java-to-kotlin)
>
> 이 글은 개인 공부용으로 작성되었으며 coursera 의  Kotlin for Java Developers 강의를 의역하였습니다.



## Nullable types

**NPE**(NullPointException)는 개발자를 괴롭혀온 유명한 문제이다. NPE는 런타임 예외로 문제를 발견하기 힘들뿐더러 코드를 수정하기도 힘들다. NPE를 해결하기 위한 최근의 접근 방법은 NPE를 런타임 예외에서 컴파일 타임 예외로 변경하는 것이다. 이러한 방식은 NPE에 대한 근본적인 해결 방법은 아니지만 NPE를 코드 작성시 확인할 수 있게 된다.

코틀린은 변수의 자료형을 `nullable type` 과 `non-nullable type` 으로 구분할 수 있다.

```kotlin
val s1: String = "always not null" // non-nullable
val s2: String? = null // nullable
```

위의 방식으로 정의된 변수는 항상 non-nullable 한 객체만을 가진다. non-nullable 한 변수를 `null` 로 초기화 한다면 코틀린 컴파일러가 이를 인지하고 수정을 요구한다. 변수가 null 값을 가질 수 있게 하려면 변수의 자료형 뒤에 물음표 기호를 추가하여 nullable 한 변수임을 명시해 주어야 한다.

non-nullable 하게 선언된 변수에 대한 접근은 이미 해당 변수가 null 을 가질 수 없다는 점이 보장되어 있기 때문에 아무런 문제 없이 접근이 가능하다. 하지만 nullable 한 변수에 접근은 NPE가 발생할 가능성이 있기 때문에 컴파일러에 의해 접근이 차단된다. nullable 변수에 대해 접근하기 위해서는 해당 변수가 null 이 아니라는점이 명시되어야 한다.

```kotlin
val s: String?

if (s != null)
    s.length
```

코틀린에서는 이러한 방식의 접근을 간소화한 문법을 제공한다.

**Safe access**

nullable 객체에 안전하게 접근하기 위해 코틀린은 `?.` 를 이용해 **safe call** 연산자를 제공한다

```kotlin
val s: String?

val length = if (s != null) s.length else null
```

```kotlin
val length = s?.length
```

위의 두 코드는 정확히 동일한 작업을 수행한다.

nullable 객체에 대한 safe access 는 내부적으로 먼저 해당 객체가 null 인지 확인하고 null 이 아닐 경우에만 요청한 결과를 반환한다. 만약 객체가 null 이라면 해당 객체에 접근하지 않고 단순히 null 을 반환한다. 이 때 `length` 의 자료형은 대입되는 값이 null 일 가능성이 있기 때문에 `Int?` 가 된다. non-nullable 하게 만들기 위해서는 `if` 구문에서 명시적으로 null 이 할당되지 않는다는것을 표현해야한다.

코틀린에서는 nullable 한 결과에 대해 기본값을 할당해주는 **엘비스 연산자**를 제공한다.

```kotlin
val length = if (s != null) s.length else 0

val length = s?.length ?: 0
```

위의 두 라인은 동일한 작업을 수행한다. 엘비스 연산자는 `?:` 를 사용하며 만약 연산자의 좌항이 null 인 경우에는 우항의 값을 `default` 로 반환한다.

또한 코틀린에서는 NPE의 발생을 명시적으로 표현하는 `not-null assertion` 을 제공한다. 두 개의 느낌표로 구성된 연산자는 피 연산자가 null 인 경우에는 NPE를 발생시키고 그렇지 않다면 피연산자를 반환한다.

```kotlin
if (s == null)
    throw NullPointerException()
println(s.length)

println(s!!.length)
```

`not-null assertion` 이후에는 피연산자가 `non-nullable type` 으로 smart casting 되기 때문에 변수에 대한 접근이 가능하다. 이 연산자를 이용하면 NPE가 발생했을 때 문제점을 특정할 수 있다는 점에서 유용하므로 무분별한 사용은 아무런 의미를 가지지 않는다.

```kotlin
val s: String? = a!!.b!!.c!!.d // 안좋은 사용의 예
```



## Safe casts

`?` 는 nullable 변수에 대한 safe access 뿐만 아니라 safe cast 에도 사용된다. 자바에서는 타입 캐스팅 시 서로간의 자료형이 맞지 않으면 예외가 발생하고 이를 방지하기 위해서는 변환을 위한 명시적인 비교가 필요하다.

```java
if (a instanceof String) {
    String s = (String)a;
    s.toUpperCase();
}
```

코틀린에서는 위의 코드를 다음과 같이 표현할 수 있다.

```kotlin
if (any is String) {
    val s = any as String
    s.toUpperCase()
}
```

사실 이 코드에서 `val s = any as String` 라인은 불필요 하다. `if` 의 조건에서 `any` 가 `String` 으로 변환될 수 있다는것이 보장되므로(smart cast) `if` 의 본체는 `any.toUpperCase()` 만으로도 충분하다.

코틀린의 **safe cast** 는 이러한 구문의 개선된 문법을 제공한다.

```kotlin
(any as? String)?.toUpperCase()
```

이 코드는 이전의 코드와 동일한 결과를 제공한다.

**safe cast** 는 좌항이 우항으로 변환될 수 있다면 우항으로 변환된 좌항을 반환하며 변환이 불가능 하다면 null 을 반환한다. 동일한 동작을 하는 `if` 문과 safe cast를 비교해보자.

```kotlin
val s = if (a is String) a else null

val s: String? = any as? String
```

