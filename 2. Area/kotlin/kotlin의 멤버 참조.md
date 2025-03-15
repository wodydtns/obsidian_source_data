멤버 참조는 Kotlin에서 함수나 프로퍼티를 직접 호출하지 않고 참조로 전달할 수 있게 해주는 기능입니다. 이를 통해 함수형 프로그래밍을 더 간결하고 표현력 있게 작성할 수 있습니다.

## 기본 문법

멤버 참조는 이중 콜론(`::`) 연산자를 사용하여 표현합니다:
```kotlin
클래스::멤버
```
## 멤버 참조의 종류

### 1. 함수 참조 (Function References)

클래스의 메서드나 최상위 함수를 참조할 수 있습니다.
```kotlin
// 최상위 함수 참조
fun isEven(num: Int): Boolean = num % 2 == 0

val numbers = listOf(1, 2, 3, 4, 5)
val evenNumbers = numbers.filter(::isEven)  // [2, 4]

// 클래스 메서드 참조
class Person(val name: String, val age: Int) {
    fun isAdult(): Boolean = age >= 18
}

val people = listOf(Person("Alice", 20), Person("Bob", 15))
val adults = people.filter(Person::isAdult)  // [Person("Alice", 20)]
```

### 2. 프로퍼티 참조 (Property References)

클래스의 프로퍼티나 최상위 프로퍼티를 참조할 수 있습니다.
```kotlin
val Person.nameLength get() = name.length

val people = listOf(Person("Alice", 20), Person("Bob", 15))
val names = people.map(Person::name)  // ["Alice", "Bob"]
val nameLengths = people.map(Person::nameLength)  // [5, 3]
```
### 3. 생성자 참조 (Constructor References)

클래스의 생성자를 참조할 수 있습니다.
```kotlin
data class User(val name: String, val age: Int)

val createUser = ::User
val user = createUser("Alice", 25)  // User(name="Alice", age=25)

// 리스트에서 객체 생성
val userNames = listOf("Alice", "Bob", "Charlie")
val users = userNames.map { ::User(it, 20) }
```
### 4. 바운드 멤버 참조 (Bound Member References)

Kotlin 1.1부터 특정 인스턴스에 바인딩된 멤버 참조를 사용할 수 있습니다.
```kotlin
val person = Person("Alice", 25)
val isAdultFunction = person::isAdult  // person 인스턴스에 바인딩
val result = isAdultFunction()  // true

val nameProperty = person::name
println(nameProperty.get())  // "Alice"
```
### 5. 확장 함수/프로퍼티 참조 (Extension Function/Property References)

확장 함수나 확장 프로퍼티도 참조할 수 있습니다.
```kotlin
fun String.isLong(): Boolean = length > 10

val isLongFunction = String::isLong
println(isLongFunction("Hello"))  // false
println(isLongFunction("This is a long string"))  // true
```

## 멤버 참조의 활용 사례

### 1. 컬렉션 처리
```kotlin
data class Product(val name: String, val price: Double, val category: String)

val products = listOf(
    Product("Laptop", 1200.0, "Electronics"),
    Product("Book", 15.0, "Books"),
    Product("Phone", 800.0, "Electronics")
)

// 프로퍼티 참조를 사용한 정렬
val sortedByPrice = products.sortedBy(Product::price)

// 프로퍼티 참조를 사용한 그룹화
val groupedByCategory = products.groupBy(Product::category)

// 프로퍼티 참조를 사용한 매핑
val productNames = products.map(Product::name)
```
### 2. 이벤트 핸들러
```kotlin
// Android에서 클릭 리스너 설정
button.setOnClickListener(this::handleClick)

fun handleClick(view: View) {
    // 클릭 처리 로직
}
```
### 3. 함수 합성
```kotlin
fun square(x: Int): Int = x * x
fun isEven(x: Int): Boolean = x % 2 == 0

val numbers = listOf(1, 2, 3, 4, 5)
val evenSquares = numbers.map(::square).filter(::isEven)  // [4, 16]
```
### 4. 리플렉션과 함께 사용
```kotlin
import kotlin.reflect.KFunction

fun process(str: String): Int = str.length

val funcRef: KFunction<Int> = ::process
println(funcRef.name)  // "process"
println(funcRef.call("Hello"))  // 5
```

## 멤버 참조의 타입

멤버 참조는 함수형 타입으로 표현됩니다:
```kotlin
// 함수 참조
val isEven: (Int) -> Boolean = ::isEven

// 프로퍼티 참조
val nameGetter: (Person) -> String = Person::name

// 생성자 참조
val userCreator: (String, Int) -> User = ::User

// 바운드 멤버 참조
val person = Person("Alice", 25)
val getPersonName: () -> String = person::name
```

## 멤버 참조와 람다 표현식 비교

멤버 참조는 종종 람다 표현식을 더 간결하게 대체할 수 있습니다:
```kotlin
// 람다 표현식
val evenNumbers = numbers.filter { it % 2 == 0 }

// 멤버 참조
val evenNumbers = numbers.filter(::isEven)

// 람다 표현식
val names = people.map { it.name }

// 멤버 참조
val names = people.map(Person::name)
```

## 멤버 참조의 장점

1. **간결성**: 코드를 더 간결하게 작성할 수 있습니다.
2. **가독성**: 의도가 더 명확하게 드러납니다.
3. **재사용성**: 함수나 프로퍼티를 재사용하기 쉽습니다.
4. **함수형 프로그래밍**: 함수형 프로그래밍 스타일을 지원합니다.