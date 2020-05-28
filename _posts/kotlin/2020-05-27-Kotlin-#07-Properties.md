---
title: "Kotlin for Java Developers Week 4 - Properties"
excerpt: "Kotlin properties"
classes: wide
categories: 
  - kotlin
tag:
  - kotlin
read_time: false
last_modified_at: 2020-05-27T23:20:00+09:00
toc: true
---

> 출처 : [coursera - Kotlin for Java Developers](https://www.coursera.org/learn/kotlin-for-java-developers/lecture/P62Ej/from-java-to-kotlin)
>
> 이 글은 개인 공부용으로 작성되었으며 coursera 의  Kotlin for Java Developers 강의를 의역하였습니다.


## Properties

자바에서와 마찬가지로 코틀린에서도 프로퍼티를 지원하지만, 코틀린은 프로퍼티를 별도의 언어적 기능으로 지원한다.

**Java property**

```java
public class JavaClass {
    private int foo = 0;
    
    public int getFoo() {
        return foo;
    }
    
    public void setFoo(int foo) {
        this.foo = foo;
    }
}
```

`field + accessors(getter or setter)` = `property`

**Kotlin property**

코틀린에서도 프로퍼티에 대한 개념은 동일하지만 문법적인 차이가 있다.

```kotlin
class KotlinClass {
    var foo = 0
}
```

내부적으로는 자바의 프로퍼티와 동일하게 구현되지만 코틀린에서는 `accessor` 를 명시할 필요가 없다. 코틀린에서는 프로퍼티를 직접적으로 사용할 수 있고 변수처럼 접근이 가능하다. 코드상에서 프로퍼티명으로 직접 호출 하여도 내부적으로는 프로퍼티의 `getter` 가 호출된다. `setter` 또한 마찬가지로, 프로퍼티의 이름으로 접근하여 단순히 값을 변경하여도 내부적으로 `setter` 가 호출된다.

코틀린에서 `val` 로 선언된 프로퍼티는 읽기 전용으로 `getter` 가 자동적으로 생성된다. 프로퍼티를 `var` 로 선언한다면 `getter` 와 `setter` 가 생성된다.

**custom getter**

아래의 방법처럼 프로퍼티의 접근자를 커스텀할 수 있다.

```kotlin
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
        get() {
            return height == width
        }
}
```

**field**

코틀린에서는 프로퍼티를 사용하기 때문에 `field` 를 사용할 필요가 없다. 하지만 필요에 따라 프로퍼티의 접근자 내에서 `field` 를 사용할 수 있다. 접근자 내에서만 사용되므로 클래스의 다른 함수에서는 사용할 수 없다.

```kotlin
class StateLogger {
    var state = false
        set(value) {
            println("state has changed: " +
                    "$field -> $value")
            field = value
        }
}
```

접근자 내부에서 `field` 키워드를 사용하여 접근할 수 있으며, `setter` 의 매개변수는 관습적으로 `value` 를 사용한다. 



## 인터페이스 프로퍼티

```kotlin
interface User {
    val nickname: String
}
```

인터페이스에서도 이처럼 프로퍼티를 정의할 수 있다. 내부적으로는 단순히 `getter` 를 제공하는 역할을 수행한다. 인터페이스를 구현하는 클래스에서 프로퍼티를 오버라이드 하여 사용한다.

인터페이스의 프로퍼티는 기본적으로 `open` 이며 `smart cast` 가 불가능 하다. `open` 이란 `final` 이 아니며 서브클래스에서 재정의할 수 있음을 말한다. 프로퍼티가 `open` 이라면 하위 클래스에서는 접근할 때 마다 수행되는 `custom getter` 가 정의되어 있어야 한다.



## 확장 프로퍼티

확장 함수와 마찬가지로 프로퍼티 또한 확장시킬 수 있다.

```kotlin
val String.lastIndex: Int
    get() = this.length - 1
val String.indices: IntRange
    get() = 0..lastIndex
```

확장된 프로퍼티는 기존의 멤버 프로퍼티처럼 접근할 수 있다.