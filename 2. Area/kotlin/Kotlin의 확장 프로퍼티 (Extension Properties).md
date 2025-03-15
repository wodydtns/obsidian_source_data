## 확장 프로퍼티의 특징

1. **백킹 필드 없음**: 확장 프로퍼티는 실제 클래스에 물리적 필드를 추가하지 않습니다. 따라서 `field` 식별자를 사용할 수 없습니다.
2. **커스텀 getter/setter**: 항상 getter와 필요시 setter를 명시적으로 정의해야 합니다.
3. **수신자 타입**: 확장하려는 클래스가 수신자 타입이 됩니다.

## 예제

### 1. 기본 확장 프로퍼티
```kotlin
val String.lastChar: Char
    get() = this[length - 1]

fun main() {
    println("Kotlin".lastChar) // 출력: n
}
```
### 2. 가변(mutable) 확장 프로퍼티
```kotlin
var StringBuilder.lastChar: Char
    get() = this[length - 1]
    set(value) {
        this.setCharAt(length - 1, value)
    }

fun main() {
    val sb = StringBuilder("Kotlin")
    println(sb.lastChar) // 출력: n
    
    sb.lastChar = '!'
    println(sb) // 출력: Kotli!
}
```
### 3. 컬렉션에 대한 확장 프로퍼티
```kotlin
val List<Int>.average: Double
    get() = if (isEmpty()) 0.0 else sum().toDouble() / size

fun main() {
    val numbers = listOf(1, 2, 3, 4, 5)
    println(numbers.average) // 출력: 3.0
}
```
## 제약 사항

1. 확장 프로퍼티는 실제 클래스에 물리적 상태를 추가하지 않기 때문에 초기화 블록을 가질 수 없습니다.
2. 확장 프로퍼티는 항상 명시적인 getter(와 setter)를 정의해야 합니다.
3. 확장 프로퍼티는 프로퍼티 위임(delegated properties)과 함께 사용할 수 있지만, 백킹 필드가 없다는 제약은 여전히 적용됩니다.
