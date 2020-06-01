---
title: "Kotlin for Java Developers Week 4 - Inline Function"
excerpt: "Kotlin Inline Function"
classes: wide
categories: 
  - kotlin
tag:
  - kotlin
read_time: false
last_modified_at: 2020-06-01T22:22:00+09:00
toc: true
---

> 출처 : [coursera - Kotlin for Java Developers](https://www.coursera.org/learn/kotlin-for-java-developers/lecture/P62Ej/from-java-to-kotlin)
>
> 이 글은 개인 공부용으로 작성되었으며 coursera 의  Kotlin for Java Developers 강의를 의역하였습니다.


## Library functions looking like built-in constructs

**run**

`run` 함수는 람다를 실행하고 람다의 마지막 식을 반환한다.

```kotlin
val foo = run {
    println("Calculating foo...")
    "foo"
}
```

**let**

`let` 함수는 함수를 호출하는 객체를 람다의 인자로 넘겨서 사용할 수 있다. `let` 을 safe call(`?.`) 과 함께 사용하면 스마트 캐스트되어 인자의 `null` 여부를 확인하지 않아도 된다.

```kotlin
fun getEmail(): Email?
val email = getEmail()

if (email != null) sendEmailTo(email)

email?.let { e -> sendEmailTo(e) }
getEmail?.let { sendEmailTo(it) }
```

**takeIf**

객체가 주어진 `predicate` 를 만족할 때 해당 객체를 반환하며, 그렇지 않으면 `null` 을 반환한다.

```kotlin
issue.takeIf { it.status == FIXED }
person.patronymicName.takeIf(String::isNotEmpty)
```

**takeUnless**

`takeIf` 와 반대되는 동작을 수행한다. 객체가 주어진 `predicate` 를 만족하지 않을 때 해당 객체를 반환하며, 만족한다면 `null` 이 반환된다.

**repeat**

주어진 횟수 만큼의 동작을 반복한다.

```kotlin
repeat(10) {
    println("Welcome!")
}
```

소개된 함수들은 모두 코틀린 라이브러리 내에 함수로 구현되어 있으며 `inline` 키워드가 붙어있기 때문에 퍼포먼스에 있어서 오버헤드가 발생하지 않는다.

**withLock**

동기화를 위해 `lock` 이 필요할 때, `synchronized` 대신 `withLock` 함수를 사용할 수도 있다.

```kotlin
val l: Lock...
l.withLock {
    // access the resource protected by this lock
}
```

**use**

자바의 `try-with-resource` 문을 대신하여 코틀린에서는 `use` 함수를 사용할 수 있다.

```kotlin
fun readFirstLineFromFile(path: String): String {
    BufferedReader(FileReader(path)).use { br ->
        return br.readLine()
    }
}
```



## power of inline

앞서 소개한 함수들이 `inline function` 이 아니라 일반적인 함수로 구현되었다면, 각각의 호출 마다 내부적으로 람다를 저장하기 위해 익명 클래스가 생성되고 이에 따른 오버헤드가 발생한다.

`inline` 함수는 함수를 호출하는것을 컴파일러가 함수의 몸체로 대치하기 때문에 추가적인 객체나 클래스의 생성이 없다. `inline` 함수의 단점은 함수의 호출을 함수의 몸체로 대치하여 바이트코드 레벨에서 프로그램의 사이즈가 커진다는 점이다.

