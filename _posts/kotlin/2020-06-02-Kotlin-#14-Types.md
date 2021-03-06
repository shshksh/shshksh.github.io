---
title: "Kotlin for Java Developers Week 5 - Types"
excerpt: "Kotlin Types"
classes: wide
categories: 
  - kotlin
tag:
  - kotlin
read_time: false
last_modified_at: 2020-06-03T14:30:00+09:00
toc: true
---

> 출처 : [coursera - Kotlin for Java Developers](https://www.coursera.org/learn/kotlin-for-java-developers/lecture/P62Ej/from-java-to-kotlin)
>
> 이 글은 개인 공부용으로 작성되었으며 coursera 의  Kotlin for Java Developers 강의를 의역하였습니다.



## Basic types

코틀린에서는 변수의 원시 타입과 참조 타입을 구분하지 않는다. 정수형 타입을 사용하는 경우에는 `nullable type` 과 `non-nullable` 타입만을 구분한다.

하지만 코틀린 코드를 바이트 코트로 변환한 후 자바 코드로 디컴파일하면 타입이 원시 타입으로 선언된 경우를 확인할 수 있다.

**Int**

```kotlin
// Kotlin
fun foo(): Int = 1

// Java
public static final int foo() {
    return 1;
}

// Kotlin
fun bar(): Int? = 1

// Java
public static final Integer bar() {
    return 1;
}
```

코틀린에서는 원시 타입이 존재하지 않지만 컴파일된 바이트 코드 레벨에서는 여전히 원시 타입이 사용된다.

`Int` 의 경우 단순한 사용은 바이트 코드 레벨에서 `int` 로 변환되지만, 제네릭 인자로 사용한다면 원시 타입을 사용할 수 없기 때문에 `Integer` 로 변환된다.

**Array**

배열의 경우에도 동일하게 적용된다.

`Int` 를 배열의 제네릭 매개변수로 사용하는 경우`(Array<Int>)`에는 `Integer[]` 로 취급된다. 하지만 `IntArray` 로 얻은 배열은 `int[]` 로 취급되는것을 확인할 수 있다.

**String**

코틀린의 문자열은 내부적으로 자바의 `String` 클래스로 변환된다. 코틀린은 문자열을 위해 독자적인 구현을 하진 않지만 자바의 문자열 API 를 조금 변형해서 사용한다.

**Any**

코틀린에서 `Any` 는 자바에서 `Object` 로 변환되며 모든 `non-nullable` 타입의 상위 타입이다. 다만 코틀린에서는 참조 타입 객체 뿐만 아니라 원시 타입`(Int...)` 에 대한 상위 타입으로 동작한다.

**Function**

코틀린의 람다들은 자바에서 동일한 제네릭 파라미터를 가지는 `interface` 로 변경된다. 매개변수를 가지는 람다의  사용은 자바에서 `invoke` 메서드를 호출하는 것으로 변경된다.

**List**

코틀린의 배열은 자바의 배열과 동일하다. 따라서 배열을 사용하면 동등성 비교 등에 있어서 기존의 코틀린 컨벤션과 일치하지 않는 결과를 얻을 수도 있다.

코틀린의 `Array` 는 자바와의 상호 운용성을 위해 사용된다. 만약 단순히 코틀린만을 사용하고 다른 자바 API 를 사용하지 않는다면 `Array` 를 사용할 필요가 없다.



## Kotlin type hierarchy

코틀린은 모든 타입에 대한 상위 타입으로 `Any` 를 가지는것 뿐만 아니라 모든 타입에 대한 하위 타입으로 `Nothing` 을 가진다.

코틀린만의 타입에 대해 자바의 `void` 와 비교했을 때 `Nothing` 과 `Unit` 에 대해 혼란이 발생한다. 이전에 소개했듯이 코틀린은 리턴 타입이 `void` 인 경우 `Unit` 을 사용하지만 이름에 있어서는 `Nothing` 이 더 어울리는것 처럼 느껴진다. 

**Unit**

코틀린에서 `Unit` 은 항상 `void` 를 대신하여 사용된다. `Unit` 은 함수의 반환값이 아무 의미가 없다는것을 나타낸다. 함수의 반환값이 필요 없는 경우 리턴 타입은 `Unit` 이 되며 명시적으로 표시하지 않는다면 기본적으로 코틀린의 함수는 `Unit` 타입을 반환한다.

**Nothing**

`Nothing` 의 경우에는 `void` 나 `Unit` 과 확실히 다르다.  `Nothing` 은 함수가 끝나지 않는다는 것을 나타낸다. `Nothing` 타입은 함수가 단지 예외를 던지는 동작만을 담당할 때 리턴 타입으로 사용된다.

함수의 리턴 타입으로 `Nothing` 을 사용하면 컴파일러가 이 함수는 일반적으로 사용되지 않는다는것을 알 수 있다.

**nullable type**

`Any`, `Int`, `User` 등의 `non-nullable` 타입은 모두 각각의 `nullable` 타입의 하위 타입이다. `nullable` 타입에는 `null` 과 `non-nullable` 타입이 할당 될 수 있다는 점에서 논리적으로 충분히 이해가 가능하다.

하지만 `Nothing` 타입 또한 `Nothing?` 의 하위 타입이다. `Nothing?` 타입은 무엇을 나타내는 타입일까?

`Nothing?` 은 `null` 로 초기화된 변수에서 찾을 수 있다. 변수의 타입을 명시하지 않고 `null` 로 초기화 한 이후 다른 값을 할당하려 한다면 변수의 타입이 `Nothing?` 이라는 에러가 나타나는것을 확인할 수 있다.



## Nullable types

자바의 자료형은 `nullable` 타입과 `non-nullable` 타입이 구분되지 않는다. 그렇다면 자바의 메서드를 어떻게 코틀린에서 호출할 수 있을까?

**@Nullable, @NotNull**

코틀린의 `nullable` 타입은 내부적으로 어노테이션을 사용해서 구현되어 있다. 자바에서 동일한 어노테이션을 사용한다면 코틀린에서는 어노테이션에 따라 `nullable`, `non-nullable` 타입으로 변환된다.

그렇다면 만약 자바에서 어노테이션을 사용하지 않았다면 코틀린에서는 어떻게 보일까?

**Platform type**

코틀린은 자바에서 어노테이션을 사용하지 않은 타입을 그냥 자바의 타입으로 이해하며 동작 또한 자바처럼 동작한다. `nullable`, `non-nullable` 둘 중 어디에도 포함되지 않는다.

종종 코틀린에서 자료형이 `Type!` 처럼 표현되는 경우를 볼 수 있다. 이것은 해당 타입이 자바로부터 왔고 `nullability` 를 확인할 수 없다는것을 의미한다.



## Collection types

이전에 소개한것처럼 코틀린 라이브러리는 자바의 컬렉션에 다수의 확장함수가 추가되었고, 코틀린에서 컬렉션을 생성하는것은 내부적으론 자바의 컬렉션을 생성하는것과 동일하다.

하지만 `read-only` 리스트와 `mutable` 리스트를 함께 자바의 컬렉션과 비교하는것은 그렇게 직관적이지 않다.

**Read-only != immutable**

리스트와 `mutable` 리스트는 모두 코틀린 컬렉션 패키지에 정의되어 있으며 `mutable` 리스트는 리스트를 상속한다. 지금까지 대부분의 경우에 `mutable` 리스트보다는 일반 리스트를 사용했지만 `read-only` 는 `immutable` 이 아니라는것을 알고 있어야 한다.

읽기 전용 리스트는 단지 `setter` 가 없는 리스트이다. 리스트 변수가 참조하는 리스트는 얼마든지 다른 참조에 의해 변경될 수 있다.

그렇다면 왜 코틀린에서는 굳이 읽기 전용 리스트와 수정 가능한 리스트를 구분하는것일까?

이유는 안정성에 있다. 읽기 전용 컬렉션은 문제가 발생하기 매우 힘들며, 심지어 불가능할 수도 있다. 대부분의 경우에 문제의 발생은 데이터가 변경될 때 발생한다. 코틀린에서 데이터의 수정은 명시적으로 `mutable` 컬렉션으로 캐스트한 후에 가능하며, 일반적인 컬렉션은 데이터의 수정을 제한하여 예기치 않은 문제가 발생하는것을 방지한다.