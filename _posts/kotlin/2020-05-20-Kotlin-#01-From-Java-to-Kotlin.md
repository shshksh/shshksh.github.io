---
title: "Kotlin for Java Developers Week 2 - From Java to Kotlin"
excerpt: "From Java to Kotlin"
classes: wide
categories: 
  - kotlin
tag:
  - kotlin
read_time: false
last_modified_at: 2020-05-20T19:50:00+09:00
toc: true
---

> 출처 : [coursera - Kotlin for Java Developers](https://www.coursera.org/learn/kotlin-for-java-developers/lecture/P62Ej/from-java-to-kotlin)
>
> 이 글은 개인 공부용으로 작성되었으며 coursera 의  Kotlin for Java Developers 강의를 의역하였습니다.

## From Java to Kotlin

코틀린은 JVM 언어로 자바의 모든 라이브러리와 프레임워크를 코틀린에서 사용할 수 있고 하나의 프로젝트에서 코틀린과 자바를 함께 사용할 수도 있다.

코틀린을 배우는데 유용한 도구 중 하나로 Java to Kotlin converter 가 있다. 이 컨버터는 자바 코드를 코틀린 코드로 변환시켜주기 때문에 언어를 배울때나 기존의 자바 애플리케이션에 통합시는데 유용하게 사용할 수 있다.

**변환 예시**

```java
public class Person {
    private final String name;
    private final int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}

```

**변환**

```kotlin
class Person(val name: String, val age: Int) 
```

내부적으로, 위의 코틀린 코드는 바이트코드 단계에서 자바 코드의 생성자와 두 개의 `getter` 가 생성되기때문에 결과적으로 두 클래스는 동일한 클래스이다.

추가적으로 코틀린 클래스에 'data' modifier 를 추가한다면 `equals, hashCode, toSting` 등의 메서드를 자동으로 생성해준다.

*val 같은 변수나 함수의 선언, 사용은 추후에 다룬다.*

**자바와의 차이점**

자바에서 `Person` 클래스의 사용은 다음과 같다.

```java
Person person = new Person("Alce", 27);
System.out.println(person.getName());
```

자바에서는 먼저 `Person` 클래스의 객체를 만들고 `getName` 메서드를 이용하여 내부 필드에 접근한다. 코들린에서는 위의 작업을 다음과 같이 표현할 수 있다.

```kotlin
val person = Person("Alice", 27)
println(person.name)
```

이 코틀린 코드의 중요한 포인트는 코틀린에서는 `new` 를 사용하지 않는다는 점이다. 코틀린에서 클래스의 인스턴스는 `new` 의 사용 없이 호출만으로 간단히 생성할 수 있다.

**코틀린 코드 변환기**

변환기(converter)는 자동으로 자바 코드를 코틀린 코드로 바꾸어 주지만 항상 최선의 결과를 나타내지는 않는다.

```java
enum Color {
    BLUE, ORANGE, RED;
}

public void updateWWeather(int degrees) {
    String description;
    Color color;
    if (degrees < 10) {
        description = "cold";
        color = Color.BLUE;
    } else if (degrees < 25) {
        description = "mild";
        color = Color.ORANGE;
    } else {
        description = "hot";
        color = Color.RED;
    }
}
```

`Color enum` 을 제외한 위의 코드의 변환 결과는 다음과 같다.

```kotlin
fun updateWWeather(degrees: Int) {
    val description: String
    val color: Color
    if (degrees < 10) {
        description = "cold"
        color = Color.BLUE
    } else if (degrees < 25) {
        description = "mild"
        color = Color.ORANGE
    } else {
        description = "hot"
        color = Color.RED
    }
}
```

변수의 선언이 `val` 로 변경된 것을 제외하면 변환된 코드가 기존의 자바 코드와 거의 동일하다는것을 볼 수 있다.

이 코드는 조금 더 "**Kotlinish**" 하게 개선될 여지가 있다. 위의 코드에서는 `description = something` 의 코드가 반복되는것을 볼 수 있다. 코틀린에서는 이러한 중복을 제거할 수 있다. 

```kotlin
fun updateWWeather(degrees: Int) {
    val (description: String, color: Color) =
        if (degrees < 10) {
            Pair("cold", Color.BLUE)
        } else if (degrees < 25) {
            Pair("mild", Color.ORANGE)
        } else {
            Pair("hot", Color.RED)
        }
}
```

코틀린에서는 값의 쌍을 이용하여 두 변수를 한번에 초기화할 수 있다. 이 코드는 조금 더 개선될 수 있다.

```kotlin
fun updateWWeather(degrees: Int) {
    val (description, color) =
        if (degrees < 10) {
            Pair("cold", Color.BLUE)
        } else if (degrees < 25) {
            Pair("mild", Color.ORANGE)
        } else {
            Pair("hot", Color.RED)
        }
}
```

코틀린에서는 문맥 상 변수의 값이 명확하다면 타입 지정을 생략할 수 있다. 생략된 타입은 초기화 구문에 기반하여 코틀린 컴파일러가 자동적으로 추론한다. 이 코드에서 `if` 구문은 코틀린에서 `when` 으로 대체할 수 있다. `when`은 자바에서의 `switch` 와 비슷하다.

```kotlin
fun updateWWeather(degrees: Int) {
    val (description, color) = when {
        degrees < 10 -> Pair("cold", Color.BLUE)
        degrees < 25 -> Pair("mild", Color.ORANGE)
        else -> Pair("hot", Color.RED)
    }
}
```

`when` 구문은 몇가지 조건을 나열하여 표현할 수 있고 두 개 이상의 `if-else` 가 반복될 때 유용하다. 

조금 더 "**Kotlinish**" 한 코드를 위해 위의 코드에서 `Pair` 를 `to` 로 대체할 수 있다. `to` 는 코틀린에서 `Pair` 를 생성하는 또다른 표현이다. 최종적으로 변환된 코드는 다음과 같다.

```kotlin
fun updateWWeather(degrees: Int) {
    val (description, color) = when {
        degrees < 10 -> "cold" to Color.BLUE
        degrees < 25 -> "mild" to Color.ORANGE
        else -> "hot" to Color.RED
    }
}
```

코틀린 코드 변환기는 유용한 툴이지만 항상 최선의 결과를 보여주지는 않는다. **코틀린스러운** 코드를 위해서는 코틀린 사용자의 추가적인 작업이 필요하다.
