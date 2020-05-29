---
title: "Kotlin for Java Developers Week 4 - Conventions"
excerpt: "Kotlin Conventions"
classes: wide
categories: 
  - kotlin
tag:
  - kotlin
read_time: false
last_modified_at: 2020-05-29T21:45:00+09:00
toc: true
---

> 출처 : [coursera - Kotlin for Java Developers](https://www.coursera.org/learn/kotlin-for-java-developers/lecture/P62Ej/from-java-to-kotlin)
>
> 이 글은 개인 공부용으로 작성되었으며 coursera 의  Kotlin for Java Developers 강의를 의역하였습니다.

## Operator overloading

**Arithmetic operation**

코틀린에서느 산술 연산자를 원시 타입이나 문자열 뿐만 아니라 커스텀 타입에 대해서도 적용시킬 수 있다. 사용하려는 연산자는 연산자를 특정하는 이름의 확장함수와 `operator` 키워드로 구현된다. 

```kotlin
operator fun Point.plus(other: Point): Point {
    return Point(x + other.x, y + other.y)
}
```

| expression | function name |
| :--------: | :-----------: |
|   a  + b   |     plus      |
|   a - b    |     minus     |
|   a * b    |     times     |
|   a / b    |      div      |
|   a % b    |      rem      |

  **단항 연산자**

단항 연산자는 매개변수가 없는 함수로 구현할 수 있다.

```kotlin
operator fun Point.unaryMInus() = Point(-x, -y)
```

| expression | function name |
| :--------: | :-----------: |
|     +a     |   unaryPlus   |
|     -a     |  unaryMinus   |
|     !a     |      not      |
|  ++a, a++  |      inc      |
|  --a, a--  |      dec      |

**할당 연산자**

`a += b` 같은 연산자는 기본적으로 `a.plus(b)` 를 사용하지만 `plusAssign` 함수가 정의되어 있다면 `a.plusAssign(b)` 가 내부적으로 사용된다.



## Conventions

**comparisons**

클래스가 `Comparable` 인터페이스를 구현하고 있다면 객체간 비교를 간단한 기호로 사용할 수 있다.

| symbol |    translated to    |
| :----: | :-----------------: |
| a > b  | a.compareTo(b) > 0  |
| a < b  | a.compareTo(b) < 0  |
| a >= b | a.compareTo(b) >= 0 |
| a <= b | a.compareTo(b) <= 0 |

**equality**

`equals` 메서드를 구현하는 클래스는 `==` 기호로 동등 비교가 가능하다.

**accessing by index**

`map` 에서 키를 인덱스로 값을 찾는 연산을 `[]` 기호를 사용하여 표현할 수 있다.

```kotlin
map[key] // get function
map[key] = value // set function
```

**in**

클래스가 `contains` 메서드를 구현하고 있다면 `in` 을 사용하여 속한 범위를 확인하는 문법이 지원된다.

**rangeTo**

`..` 을 사용하여 범위를 생성하는 연산 또한 `rangeTo` 메서드를 구현하는 경우에 사용할 수 있다.

**iterator**

`iterator` 메서드를 구현하는 클래스는 루핑이 가능하다.

**Destructuring declarations syntax**

`pair` 의 요소나 `map` 에서 `key, value` 를 `(first, second)` 처럼 분리하여 선언할 수 있다. 클래스가 `component1`, `component2` 메서드를 구현하고 있는 경우에 사용이 가능하다.

`data class` 는 `componentX` 메서드를 자동으로 생성해 주기 때문에 객체를 각각의 프로퍼티로 분리할 수 있다.