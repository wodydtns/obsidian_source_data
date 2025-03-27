Kotlin의 레이블은 코드 내에서 특정 표현식이나 제어 흐름 구문을 식별하고 참조할 수 있게 해주는 기능입니다. 주로 중첩된 루프나 람다 표현식에서 특정 위치로 흐름을 제어하기 위해 사용됩니다.

## 레이블 기본 문법

레이블은 식별자 뒤에 `@` 기호를 붙여 정의합니다
```kotlin
labelName@ 표현식
```

## 레이블의 주요 사용 사례

### 1. 반복문에서의 break와 continue

중첩된 반복문에서 특정 반복문을 대상으로 `break`나 `continue`를 사용할 때 유용합니다.
```kotlin
outerLoop@ for (i in 1..5) {
    for (j in 1..5) {
        if (i * j >= 15) {
            println("Breaking outer loop at i=$i, j=$j")
            break@outerLoop // 외부 루프를 종료
        }
        println("i=$i, j=$j")
    }
}
```

```kotlin
outerLoop@ for (i in 1..3) {
    for (j in 1..3) {
        if (j == 2) {
            continue@outerLoop // 외부 루프의 다음 반복으로 이동
        }
        println("i=$i, j=$j")
    }
}
```
### 2. 람다 표현식에서의 return

람다 표현식 내에서 `return`은 기본적으로 람다를 포함하는 함수를 반환합니다. 레이블을 사용하면 람다 표현식만 종료할 수 있습니다
```kotlin
fun processNumbers() {
    listOf(1, 2, 3, 4, 5).forEach {
        if (it == 3) return@forEach // forEach 함수명을 레이블로 사용
        println(it)
    }
    println("This will be printed")
}
```

### 3. 로컬 return vs 비로컬 return
```kotlin
// 비로컬 return (함수 전체를 종료)
fun findAndPrint(list: List<Int>) {
    list.forEach {
        if (it == 3) return // findAndPrint 함수를 종료
        println(it)
    }
    println("This will NOT be printed if 3 is in the list")
}

// 로컬 return (람다만 종료)
fun findAndPrintWithLabel(list: List<Int>) {
    list.forEach {
        if (it == 3) return@forEach // 현재 반복만 종료
        println(it)
    }
    println("This will ALWAYS be printed")
}
```

### 4. this 표현식에서의 레이블

중첩된 클래스나 확장 함수에서 특정 인스턴스를 참조할 때 사용합니다:
```kotlin
class Outer {
    inner class Inner {
        fun Int.extend() {
            val outerThis = this@Outer // Outer 클래스의 인스턴스
            val innerThis = this@Inner // Inner 클래스의 인스턴스
            val intThis = this // Int의 인스턴스 (확장 함수의 수신자)
        }
    }
}
```

### 5. 익명 함수에서의 레이블

익명 함수는 기본적으로 레이블이 필요 없이 로컬 return을 사용할 수 있습니다:
```kotlin
fun processWithAnonymousFunction() {
    listOf(1, 2, 3, 4, 5).forEach(fun(value) {
        if (value == 3) return // 익명 함수만 종료
        println(value)
    })
    println("This will be printed")
}
```

## 레이블 사용 시 주의사항

1. **가독성**: 레이블을 과도하게 사용하면 코드 가독성이 떨어질 수 있습니다.
2. **명명 규칙**: 레이블 이름은 의미가 명확하고 간결하게 지정하는 것이 좋습니다.
3. **중첩 레이블**: 동일한 이름의 레이블이 중첩되면 가장 안쪽의 레이블이 참조됩니다.

## 실용적인 예제

### 중첩된 컬렉션 처리
```kotlin
fun findPerson(people: List<List<Person>>, predicate: (Person) -> Boolean): Person? {
    people.forEach { group ->
        group.forEach { person ->
            if (predicate(person)) {
                return person // 전체 함수에서 반환
            }
        }
    }
    return null
}

fun findPersonWithLabel(people: List<List<Person>>, predicate: (Person) -> Boolean): Person? {
    var result: Person? = null
    
    search@ for (group in people) {
        for (person in group) {
            if (predicate(person)) {
                result = person
                break@search // 중첩된 모든 루프 탈출
            }
        }
    }
    
    return result
}
```

### 복잡한 조건부 처리
```kotlin
fun processRequest(request: Request) {
    validateRequest@ {
        if (!request.hasValidId()) return@validateRequest false
        if (!request.hasValidContent()) return@validateRequest false
        if (!request.isAuthorized()) return@validateRequest false
        return@validateRequest true
    }.let { isValid ->
        if (isValid) {
            // 요청 처리
        } else {
            // 오류 처리
        }
    }
}
```

