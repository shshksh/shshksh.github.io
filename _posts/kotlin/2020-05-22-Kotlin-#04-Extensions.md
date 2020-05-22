---
title: "Kotlin for Java Developers Week 2 - Extensions"
excerpt: "Kotlin Extensions"
classes: wide
categories: 
  - kotlin
tag:
  - kotlin
read_time: false
last_modified_at: 2020-05-22T20:20:00+09:00
toc: true
---

> 출처 : [coursera - Kotlin for Java Developers](https://www.coursera.org/learn/kotlin-for-java-developers/lecture/P62Ej/from-java-to-kotlin)
>
> 이 글은 개인 공부용으로 작성되었으며 coursera 의  Kotlin for Java Developers 강의를 의역하였습니다.



## 확장 함수(Extension Function)

코틀린에서는 확장 함수를 정의할 수 있다. 확장 함수는 클래스를 확장한다. 확장 함수는 클래스의 외부에서 정의되지만 마치 클래스의 멤버처럼 호출된다. 간단한 예시를 보도록 하자.

```kotlin
// MyExample.kt
fun String.lastChar() = this.get(this.length - 1)

fun main() {
    val hello = "Hello"
    println(hello.lastChar())
}
```

`lastChar` 함수는 `String` 클래스에서 정의된게 아니라 `MyExample.kt` 파일에 정의되어 있다. 하지만 함수의 호출은 마치 `String` 클래스에 정의된 멤버 함수 처럼 호출하여 사용할 수 있다.

확장 함수를 호출하는 주체는 `receiver` 라고 불린다. 위의 예제에서 리시버는 문자열 객체이며 이 객체는 `this` 키워드를 사용하여 참조할 수 있다. 확장 함수 몸체(body)에서는 `this` 를 생략할 수 있다. 따라서 `lastChar` 함수는 다음과 같이 변경할 수 있다.

```kotlin
fun String.lastChar() = get(length - 1)
```

**확장 함수의 사용 범위**

확장 함수를 사용하는데 있어 중요한 포인트는 확장 함수를 정의하더라도 어디서든 확장 함수를 호출 할수는 없다는점이다. 확장 함수를 외부에서 사용하기 위해서는 명시적으로 `import` 가 필요하다. 기본적으로 확장 함수는 프로젝트 전반적으로 접근할 수 없다.

코틀린의 확장 함수가 자바에서는 어떻게 표현되는지가 사용 범위를 이해하는데 있어 도움이 될 수 있다. 이전에 코틀린에서 최상위 함수를 정의한것 처럼 확장 함수 또한 자바에서는 `static method` 로 호출된다. 이 때, 확장 함수의 리시버를 메서드의 첫 번째 매개변수로 사용한다. 따라서 `lastChar` 함수는 다음과 같이 호출된다.

```kotlin
String hello = "Hello";
System.out.println(MyExample.lastChar(hello));
```

**필드 접근**

확장 함수가 클래스의 내부 멤버인것처럼 호출된다면 확장 함수에서 클래스의 `private` 멤버에 접근할 수도 있을까? 당연히 **접근할 수 없다**. 앞서 얘기한것 처럼 확장 함수는 클래스 외부의 `static method` 로 표현되기 때문에 클래스의 `private` 멤버는 클래스 내부에서만 접근 가능 하다. 

확장 함수는 단지 사용에 있어서 편의성을 제공할 뿐 추가적인 접근 권한은 부여하지 않는다.



## 익스텐션 호출

이 파트에서는 서로 상속 관계에 있는 클래스들에 대한 확장 함수는 어떻게 호출되는지 알아보려 한다.

```kotlin
open class Parent
class Child: Parent

fun Parent.foo() = "parent"
fun Child.foo() = "child"

fun main() {
    val parent: Parent = Child()
    println(parent.foo())
}
```

확장 함수가 `static method` 로 표현된다는점을 기억한다면 `"parent"` 가 출력됨을 쉽게 알 수 있다. 위의 코드를 자바로 변환하면 다음과 같다.

```java
public static String foo(Parent parent) { return "parent"; }

public static String foo(Child child) { return "child"; }

public static void main(String[] args) {
    Parent parent = new Parent();
    System.out.println(foo(parent));
}
```

컴파일러는 호출할 메서드를 선택할 때 매개변수의 자료형만을 보고 메서드를 선택한다. 매개변수가 무슨 데이터를 가지고 있는지는 관여하지 않는다.

이전에 말한것 처럼 확장 함수는 `static method` 로 작동하기 때문에 기존의 함수를 `override` 하지 않는다. 그렇다면 확장 함수명이 클래스의 멤버 함수와 겹친다면 호출 시 어느 함수가 선택되는걸까?

결론부터 말하자면 멤버 함수와 확장 함수가 중복되는 경우 항상 멤버 함수가 우선적으로 선택된다. 하지만 함수의 시그니처가 변하는 경우(매개변수의 수, 자료형 등..)에는 멤버 함수와 확장 함수 중 더욱 알맞는 함수가 호출된다.

