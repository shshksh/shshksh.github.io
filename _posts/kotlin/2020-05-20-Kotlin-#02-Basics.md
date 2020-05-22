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



## Hello, World!

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

이 코드는 `args` 배열이 비어있지 않다면 `name` 배열의 첫 번째 값을 가져와서 저장하고 배열이 비어있다면 "Kotlin" 문자열을 저장한다. 코틀린에서는 배열을 위해 내장된 문법이 존재하지 않는다. 바이트코드 단계에서 Array 클래스는 자바에서의 배열과 동일하다.

코틀린에서 `if` 는 **식**으로 작동한다. `if` 의 결과를 변수에 할당할 수 있고 함수의 `return` 에 사용할 수도 있다.

**String**

코틀린 뛰어난 문자열 문법을 제공한다. `$` 기호와 변수명을 사용하여 문자열에 특정 변수의 값을 삽입할 수 있다. 또한 `${functioncall()}` 와 같이 함수의 결과를 문자열에 넣을 수도 있다. 자바에서 처럼 변수의 값이나 함수의 결과를 출력하기 위해 `+` 로 문자열을 일일이 연결하지 않아도 된다.

## 변수

코틀린에서 변수의 선언을 위해 `val` 과 `var` 라는 두 가지의 키워드를 제공한다. `val` 은 value 에서 온 키워드로 read-only variable 이다. `val` 로 선언된 변수에는 값을 한 번만 할당할 수 있고 이 값은 변경할 수 없다.  `val ` 은 자바의 `final` 과 동일하다. `var` 는 variable 에서 온 키워드로 변경할 수 있는 변수를 의미한다. 

코틀린에서는 문맥상 변수에 지정되는 데이터가 명확하다면 변수의 자료형을 생략할 수 있다. 생략된 자료형은 컴파일러가 추론하여 정한다. 자료형을 명시하지 않아도 값을 지정하면 자료형이 정해지기 때문에 같은 변수에 다른 자료형의 데이터를 지정할 수 없다. 따라서 다음의 코드는 컴파일 에러가 발생한다.

```kotlin
var string = 1 // 자료형이 Int로 추론됨
string = "abc" // Int형 변수에 문자열을 지정할 수 없음
```

자료형을 생략하더라도 변수가 초기화 되지 않는다면 자료형을 생략할 수 없다. 이 때는 자료형을 명시적으로 지정해 줘야 한다.

```kotlin
var num: Int
num = 10
```

기본적으로 변수의 선언은 `val` 로 하고 값의 변경이 필요한 경우에만 `var` 로 선언하는것이 권장된다.

## 함수

코틀린에서 함수의 형태는 기본적으로 다음과 같다.

```kotlin
fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}
```

여기서는 함수가 받는 매개변수의 자료형이 매개변수명 뒤에 지정되고 함수의 리턴타입이 `Int` 임을 알 수 있다. 만약 위의 코드처럼 함수가 단순히 하나의 리턴문이라면 코틀린에서는 `function with expression body` 라 불리는 문법을 적용할 수 있다.

```kotlin
fun max(a: Int, b: Int): Int = if (a > b) a else b
```

함수의 반환값이 명확하다면 반환값의 자료형을 생략할 수 있다. 함수의 반환값이 없는 경우에는 내부적으로 반환값의 자료형이  `Unit` 으로 지정된다. 명시적으로 표현할 수도 있지만 권장되지 않는다. 

코틀린에서 함수는 어디서든 정의할 수 있다. 이전에 본것과 같이 최상위 함수로 정의할 수도 있고 클래스의 멤버로 정의할 수도 있으며 심지어 함수 내부에서도 `Local function` 을 정의하여 사용할 수도 있다.

최상위 함수로 정의된 함수는 자바에서 파일명과 동일한 클래스의 `static method` 로 표현된다. 

```kotlin
// MyFile.kt
package example

fun foo() = 0
```

이 함수는 자바에선 다음의 코드와 동일하다.

```java
public class MyFile {
    static int foo() {
        return 0;
    }
}
```

또한 코틀린 파일에서 `JvmName` 어노테이션을 사용하여 최상위 함수를 `static method` 로 가지는 클래스 명을 변경할 수 있다.

```kotlin
@file:JvmName("Util")
package example

fun foo() = 0
```

이 함수는 자바에서 `Util.foo()` 와 같이 지정한 클래스명으로 호출할 수 있다.

## 매개변수

```kotlin
println(listOf('a', 'b', 'c').joinToString("", "(", ")"))
```

`joinToString` 함수는 구분자와 접두어, 접미어를 매개변수로 받아서 문자열을 만들어주는 함수이다. 코틀린에서는 코드에서 함수의 매개변수를 직접적으로 특정할 수 있다. 이러한 방식으로 인자를 명시적으로 표시해주면 매개변수 입력의 순서를 변경해서 함수를 호출할 수도 있다.

```kotlin
println(listOf('a', 'b', 'c').joinToString(separator = "", prefix = "(", postfix = ")"))
```

**named argument**는 **default argument**와 함께 사용된다. `joinToString` 함수에서는 모든 매개변수를 지정해줄 필요가 없다. 기본 매개변수 값이 있기 때문에 필요가 없다면 지정하지 않아도 된다.

```kotlin
println(listOf('a', 'b', 'c').joinToString(postfix = "."))
```

이 코드의 실행 결과는 **a, b, c.** 이다. 따라서 `joinToString` 함수의 separator 는 기본적으로 ", " 가 지정된다는것을 알 수 있다. 기본 매개변수를 사용한 함수는 다음과 같이 정의할 수 있다.

```kotlin
fun displaySeparator(character: Char = '*', size: Int = 10) {
    repeat(size) {
        print(character)
    }
}
```

위의 함수는 기본 매개변수가 지정되어 있기 때문에 매개변수를 지정하지 않는다면 "\*\*\*\*\*\*\*\*\*\*" 가 출력된다는 것을 알 수 있다. 자바에서는 동일한 수행 결과를 얻기 위해 메서드를 overload 해야 했지만 코틀린에서는 기본 매개변수를 사용하여 함수를 overload 하면서 생기는 중복을 제거할 수 있다.



