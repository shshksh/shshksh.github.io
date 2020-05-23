---
title: "Kotlin for Java Developers Week 3 - Functional Programming"
excerpt: "Kotlin Functional Programming"
classes: wide
categories: 
  - kotlin
tag:
  - kotlin
read_time: false
last_modified_at: 2020-05-23T23:20:00+09:00
toc: true
---

> 출처 : [coursera - Kotlin for Java Developers](https://www.coursera.org/learn/kotlin-for-java-developers/lecture/P62Ej/from-java-to-kotlin)
>
> 이 글은 개인 공부용으로 작성되었으며 coursera 의  Kotlin for Java Developers 강의를 의역하였습니다.



## 람다

람다는 식으로 사용될 수 있는 익명 함수를 말한다. 예를 들어 람다는 함수 호출 시 인자로 넘겨 줄 수도 있다. 람다는 자바의 익명 클래스와 동일한 역할을 하며, 자바 또한 자바 8 이후로 람다를 지원하고 있다. 람다는 간결한 함수적 문법을 제공하기 때문에 대다수의 모던 프로그래밍 언어에서 람다식을 지원해주고 있다.

특히 람다식은 컬렉션을 사용할 때 함수적 표현을 제공해준다.

**컬렉션에서의 람다 사용 예시**

```kotlin
data class Employee(
        val city: City,
        val age: Int
)

fun main() {
    val employee: List<Employee>

    employee.filter { it.city == City.PRAGUE }
            .map { it.age }
            .average()
}
```

**람다 문법**

코틀린에서 람다는 항상 중괄호로 둘러싸인다. `if` 문과 같이 중괄호를 사용하는 다른 구문들과 구분하기 위해 개발 툴은 람다식을 굵은 중괄호로 강조한다.

```kotlin
{ x: Int, y: Int -> x + y }
```

중괄호의 내부는 먼저 람다식의 매개변수가 위치한다. 매개변수 다음 위치한 화살표 기호 이후에 람다식의 본체가 위치한다. 람다식을 인자로 사용하는 경우에는 람다식 전체를 괄호 내에 넣을 수 있다.

```kotlin
list.any({i: Int -> i > 0})
```

이러한 람다식의 사용은 다른 표현 방법도 제공된다. 먼저 람다식이 함수의 매개변수 중 마지막 매개변수인 경우엔 람다식을 괄호 밖으로 빼낼 수 있다.

```kotlin
list.any() {i: Int -> i > 0}
```

람다식이 호출하는 함수의 유일한 매개변수라면 함수 호출 시 괄호를 생략할 수 있다.

```kotlin
list.any {i: Int -> i > 0}
```

람다식의 매개변수가 문맥상 명확하게 추론할 수 있다면, 매개변수의 타입을 생략할 수 있다.

```kotlin
list.any {i -> i > 0}
```

람다식의 매개변수가 하나밖에 없다면 매개변수의 이름을 `it` 으로 대체할 수 있다.

```kotlin
list.any {it > 0}
```

위의 다섯 개의 표현방법은 모두 동일한 동작을 수행한다.

람다식 내부에서 추가적인 동작이나 복잡한 로직을 표현하고 싶다면 단순히 중괄호 내부에서 몇 줄의 코드를 사용하면 된다. 중괄호 내부의 마지막 라인이 람다식의 반환값이 된다.

```kotlin
list.any {
    println("$it")
    it > 0
}
```

람다에서 매개변수가 `map` 처럼 값의 쌍을 가진다면 `destructuring syntax` 를 적용할 수 있다. 예를 들어 다음의 두 라인은 동일한 표현이다.

```kotlin
map.mapValues { entry -> "${entry.key} -> ${entry.value}!" }

map.mapValues { (key, value) -> "$key -> $value!" }
```

이 중 람다식 본체에서 사용하지 않는 매개변수가 있다면 단순히 그 매개변수의 이름을 `_` 로 표현하여 명명을 생략할 수 있다.

```kotlin
map.mapValues { (_, value) -> "$value!" }
```



## 컬렉션에서의 람다

**filter**

`filter` 는 `predicate` 를 매개변수로 컬렉션의 요소 중 `predicate` 를 만족하는 요소만 걸러 새로운 컬렉션을 만든다.

```kotlin
fun main() {
    val list = listOf("a", "b", "c", "A", "B", "C")

    list.filter { it in "a".."z" }
            .forEach { print("$it ") }
}

// 출력 결과: a b c
```

**map**

`map ` 은 컬렉션의 요소를 변환시켜 새로운 컬렉션을 만든다. 만들어진 컬렉션의 사이즈는 기존의 컬렉션의 사이즈보다 커질 수 있다.

```kotlin
fun main() {
    val list = listOf("a", "b", "c", "A", "B", "C")

    list.map { it.toUpperCase() }
            .forEach { print("$it ") }
}

// 출력 결과: A B C A B C 
```

**any, all, none**

컬렉션을 순회하며 요소들을 체크한 결과를 반환한다. `predicate` 를 매개변수로 받으며 `any` 는 컬렉션의 요소 중 하나라도 조건을 만족한다면 `true` , `all` 은 컬렉션의 모든 요소가 조건을 만족할 때 `true` , `none` 은 컬렉션의 모든 요소가 조건을 만족하지 않을 때 `true` 를 반환한다.

```kotlin
fun main() {
    val list = listOf("a", "b", "c", "A", "B", "C")

    print(list.any { it == "A" })

    print(list.all { it in "a".."z" || it in "A".."Z" })

    print(list.none { it.contains("d") })
}

// 출력 결과: true true true
```

**find**

`find` 는 `predicate` 를 매개변수로 받아 조건을 만족하는 요소를 찾아 반환한다. 컬렉션에서 조건을 만족하는 요소가 하나도 없다면 `null` 을 반환한다.

```kotlin
fun main() {
    val list = listOf("a", "b", "c", "A", "B", "C")

    println(list.find { it == "B" })
}

// 출력 결과: B
```

`find` 와 비슷한 함수로 `firstOrNull` 과 `first`  를 사용할 수도 있다. `firstOrNull` 은 `find` 와 동일한 역할을 수행하며 `first` 는 조건을 만족하는 요소가 없다면 NPE를 발생시킨다.

**count**

`count` 는 컬렉션을 순회하며 조건을 만족하는 요소들의 개수를 반환한다.

**partition**

`partition` 은 컬렉션을 분할할 때 사용된다. `predicate` 를 매개변수로 받아 기존의 컬렉션의 요소 중 조건을 만족하는 요소로 구성된 컬렉션과 그 이외의 컬렉션으로 분할해준다.

```kotlin
fun main() {
    val list = listOf("a", "b", "c", "A", "B", "C")

    val pair = list.partition { it in "a".."z" }
    for (s in pair.first) {
        print("$s ")
    }
    println()
    for (s in pair.second) {
        print("$s ")
    }
}
// a b c
// A B C
```

**groupBy**

컬렉션을 두 개 이상의 컬렉션으로 분할해야 할 경우에는 `groupBy` 를 사용한다. 함수의 매개변수로는 컬렉션을 어떻게 나눌지에 대한 `key` 를 넘겨줘야 한다. 함수의 반환값은 그룹핑에 사용되는 `key` 와 `key` 를 사용하여 그룹핑한 컬렉션을 `value` 로 가지는 `map` 이 반환된다.

```kotlin
fun main() {
    val list = listOf("a", "b", "c", "A", "B", "C")

    val map = list.groupBy { it.toLowerCase() }
    for ((key, value) in map) {
        for (s in value) {
            print("$s ")
        }
        println()
    }
}
/*
a A 
b B 
c C
*/
```

**associateBy**

만약 그룹핑에 사용되는 `key` 가 유일하다면, `associateBy` 가 유용할 수 있다. `associateBy` 는 `key` 에 해당하는 컬렉션의 요소를 `value` 로 가지는 `map` 을 반환한다. 만일 `unique` 하지 않은 `key` 를 사용한다면 기존에 존재하던 요소를 덮어쓰게 되므로 사용에 주의가 필요하다.

**associate**

`associate` 는 컬렉션의 요소에 기초한 `map` 을 생성할 수 있다. 

```kotlin
fun main() {
    val list = listOf(0, 1, 2, 3, 4)

    for ((key, value) in list.associate { 'a' + it to it * 10 }) {
        print("$key, $value ")
    }
}

// a, 0 b, 10 c, 20 d, 30 e, 40
```

**zip**

`zip` 은 두 개의 컬렉션을 하나의 컬렉션으로 합쳐준다. 합쳐진 컬렉션은 요소로 `Pair` 를 가지며, 기존의 컬렉션의 요소를 첫 번째 값으로, 매개변수로 넘겨진 컬렉션의 요소를 두 번째 값으로 가진다. 합쳐진 컬렉션의 사이즈는 두 컬렉션 중 작은 컬렉션의 사이즈를 따라가며 초과되는 요소는 무시한다.

**zipWithNext**

`zipWithNext` 도 마찬가지로 `Pair` 를 요소로 가지는 컬렉션을 반환하지만 기존의 컬렉션에서 이웃한 요소들을 가지고 새로운 컬렉션을 생성한다.

**flatten**

`flatten` 은 컬렉션을 요소로 가지는 컬렉션을 결합해준다. 요소로 가지는 컬렉션의 모든 요소를 결합하여 만들어진 새로운 컬렉션을 반환한다.



## Function type

자바에서의 람다식은 함수적 인터페이스의 익명 객체를 생성한다. 그렇다면 코틀린에서 람다의 자료형은 무엇일까? IDE에서 람다식을 변수에 할당하고 타입 힌트를 본다면 다음과 같이 표시된다.

```kotlin
val isEven: (Int) -> Boolean = {i: Int -> i % 2 == 0 }
```

람다의 자료형이 `(Int) -> Boolean` 으로 표시되는것을 볼 수 있다. 람다의 타입과 람다가 수행한 결과의 타입이 구분됨을 인지해야 한다. 위의 코드에서 람다는 `Int` 를 매개변수로 받아 `Boolean` 결과를 어떻게 도출하는지를 나타내는 함수이고, 반면에 람다의 결과는 단순히 `Boolean` 값이다.

이렇게 람다식이 저장된 변수는 람다식에서 필요한 매개변수를 주어 일반적인 함수처럼 사용할 수 있다. 심지어 람다식 뒤에 괄호를 붙여 직접적으로 사용할 수도 있다. 하지만 이러한 표현 방식은 어색하기 때문에 람다식 앞에 `run` 을 붙여서 사용할 수 있다.

```kotlin
val isEven: (Int) -> Boolean = {i: Int -> i % 2 == 0 }

val result = isEven(42) // true

{println("hey")}()
run {println("hey")}
```

**nullability**

`() -> Int? vs (() -> Int)?`. 왼쪽과 오른쪽의 타입은 어떻게 다른것일까? 왼쪽의 경우 매개변수가 없는 람다로 람다가 반환하는것 값은 `nullable Int` 이다. 오른쪽의 경우 마찬가지로 매개변수가 없는 람다이지만 `null` 을 반환할 수 없다. 하지만 해당 타입에 할당되는 람다가 `null` 일 수 있다(nullable lambda).

만약 `f` 라는 람다가 `nullable` 하다면 어떻게 호출할 수 있을까? 람다가 `nullable` 이기 때문에 단순히 `f()` 와 같이 호출하는것은 불가능 하다. `if` 로 람다 `f` 가 `null` 이 아닌 경우에만 `f()` 를 호출하도록 명시하거나 `f?.invoke()` 처럼 호출할 수 있다.



## Member reference

자바에서와 마찬가지로 코틀린에서도 멤버 참조가 가능하다. 맴버 참조를 사용하면 멤버 함수나, 멤버 프로버티를 사용하여 람다를 대체할 수 있다. 멤버 참조는 자바에서와 마찬가지로 `Class::member` 의 형식으로 사용된다.

이전에 소개한것 처럼, 람다는 변수에 할당할 수 있지만 함수는 변수에 할당할 수 없다. 하지만 이러한 문제는 함수 참조로 해결할 수 있다. 

```kotlin
fun isEven(i: Int) = i % 2 == 0

val i = ::isEven
```



## Return from Lambda

```kotlin
fun duplicateNonZero(list: List<Int>): List<Int> {
    return list.flatMap {
        if (it == 0) return listOf()
        listOf(it, it)
    }
}
println(duplicateNonZero(listOf(3, 0, 5)))
```

이 코드는 리스트를 받아서 0이 아닌 요소를 하나씩 추가하는 일을 한다고 보인다. 하지만 코드를 실행해보면 함수가 `empty list` 를 반환하는것을 볼 수 있다.

이 코드의 문제점은 `if (it == 0) return listOf()` 에 있다. 요소가 0인 경우에 실행하는 `return` 의 대상은 `flatMap` 이 아니라 `duplicateNonZero` 가 된다.

```kotlin
fun containsZero(list: List<Int>): Boolean {
    for (i in list) {
        if (i == 0) return true
    }
    return false
}
```

위의 코드를 본다면 반복문 내부의 리턴문이 함수를 종료시킨다는 것을 쉽게 알 수 있다. 이전의 `duplicateNonZero` 에서도 리턴문이 위의 코드와 동일하게 동작하기 때문에 `empty list` 가 반환된다. 그렇다면 람다 내부에서 리턴은 어떻게 할 수 있을까? `lable return` 을 사용하면 리턴의 대상을 람다로 지정할 수 있다.

**label return**

```kotlin
list.flatMap {
    if (it == 0) return@flatMap listOf<Int>()
    listOf(it, it)
}
```

`return@label` 의 형식으로 리턴의 대상을 지정할 수 있다. `label` 은 기본적으로 람다를 호출한 함수를 지정해줄 수 있지만 다음과 같이 원하는 `label` 을 만들 수 도 있다.

```kotlin
list.flatMap l@{
    if (it == 0) return@l listOf<Int>()
    listOf(it, it)
}
```

이처럼 이전의 `duplicateNonZero` 함수에서 `flatMap` 내부의 반복문을 `return@flatMap` 으로 변경한다면 원하는 결과가 출력된다.

또한 `local function` 을 사용하여 동일한 동작을 수행할 수 있다. 지역 함수 내부의 리턴의 대상은 수행중인 지역 함수가 된다.

```kotlin
fun duplicateNonZero(list: List<Int>): List<Int> {
    fun duplicateNonZeroElement(e: Int): List<Int> {
        if (e == 0) return listOf()
        return listOf(e, e)
    }
    return list.flatMap(::duplicateNonZeroElement)
}
```

익명 함수를 이용하여 동일한 동작을 할 수도 있다.

```kotlin
fun duplicateNonZero(list: List<Int>): List<Int> {
    return list.flatMap(fun (e: Int): List<Int> {
        if (e == 0) return listOf()
        return listOf(e, e)
    })
}
```

