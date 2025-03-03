Kotlin은 널 안전성(Null Safety)을 언어 차원에서 지원하여 NullPointerException을 방지하는 다양한 기능을 제공합니다. 그 중에서도 안전한 호출 연산자(`?.`)와 엘비스 연산자(`?:`)는 널 값을 안전하게 처리하는 핵심 기능입니다.

## 안전한 호출 연산자 (`?.`)

안전한 호출 연산자는 객체가 null이 아닌 경우에만 메서드를 호출하거나 프로퍼티에 접근합니다. 객체가 null이면 전체 표현식이 null을 반환합니다.
```kotlin
// 기본 사용법
val name: String? = null
val length: Int? = name?.length // null 반환

// 체이닝 사용
data class Address(val street: String?, val city: String?)
data class Person(val name: String, val address: Address?)

val person: Person? = getPerson() // null일 수 있는 Person 반환 함수
val city: String? = person?.address?.city // 안전하게 체이닝

// 컬렉션에서 사용
val list: List<String>? = null
val size: Int? = list?.size // null 반환

// 메서드 호출
val upperName: String? = name?.toUpperCase() // null 반환
```
## 엘비스 연산자 (`?:`)

엘비스 연산자는 좌변이 null이 아니면 좌변 값을, null이면 우변 값을 반환합니다. 이 연산자는 기본값을 제공하거나 null 처리 로직을 간결하게 표현할 때 유용합니다.
```kotlin
// 기본 사용법
val name: String? = null
val nonNullName: String = name ?: "Unknown" // "Unknown" 반환

// 예외 발생
val nonNullName2: String = name ?: throw IllegalArgumentException("Name required")

// 조기 반환과 함께 사용
fun getLength(str: String?): Int {
    return str?.length ?: return 0
}

// 중첩 사용
val value: String? = null
val defaultValue: String? = null
val result: String = value ?: defaultValue ?: "Default" // "Default" 반환
```
## 안전한 호출과 엘비스 연산자 조합

두 연산자를 함께 사용하면 null 처리를 더욱 간결하게 할 수 있습니다.
```kotlin
// 안전한 호출 + 엘비스 연산자
val name: String? = null
val length: Int = name?.length ?: 0 // 0 반환

// 체이닝 + 기본값
val person: Person? = null
val cityName: String = person?.address?.city ?: "Unknown City"

// 컬렉션 처리
val list: List<String>? = null
val nonEmptyList: List<String> = list ?: emptyList()

// 함수 호출 결과 처리
fun findUser(id: String): User? { /* ... */ }
val user: User = findUser("123") ?: createDefaultUser()
```
## 실제 사용 예제

### 1. 사용자 정보 표시
```kotlin
data class User(val id: String, val name: String?, val email: String?)

fun displayUserInfo(user: User?) {
    // user가 null이면 "Guest" 출력
    val displayName = user?.name ?: "Guest"
    
    // email이 null이면 "No email provided" 출력
    val contactInfo = user?.email ?: "No email provided"
    
    println("사용자: $displayName")
    println("연락처: $contactInfo")
}

fun main() {
    val user1: User? = User("1", "홍길동", "hong@example.com")
    val user2: User? = User("2", null, "kim@example.com")
    val user3: User? = null
    
    displayUserInfo(user1) // 사용자: 홍길동, 연락처: hong@example.com
    displayUserInfo(user2) // 사용자: Guest, 연락처: kim@example.com
    displayUserInfo(user3) // 사용자: Guest, 연락처: No email provided
}
```

### 2. 설정 값 로드
```kotlin
data class Config(val host: String?, val port: Int?, val timeout: Int?)

fun loadDatabaseConfig(): Config? {
    // 설정 파일에서 로드하는 로직
    return Config("localhost", null, 5000)
}

fun connectToDatabase() {
    val config = loadDatabaseConfig()
    
    // 기본값 적용
    val host = config?.host ?: "127.0.0.1"
    val port = config?.port ?: 3306
    val timeout = config?.timeout ?: 30000
    
    println("연결 중: $host:$port (제한시간: ${timeout}ms)")
}
```
### 3. 중첩된 객체 구조 처리
```kotlin
data class Address(val street: String?, val city: String?, val zipCode: String?)
data class Company(val name: String, val address: Address?)
data class Employee(val name: String, val company: Company?)

fun getEmployeeLocation(employee: Employee?): String {
    return employee?.company?.address?.city ?: "Unknown Location"
}

fun main() {
    val address1 = Address("123 Main St", "Seoul", "12345")
    val company1 = Company("ABC Corp", address1)
    val employee1 = Employee("홍길동", company1)
    
    val employee2 = Employee("김철수", Company("XYZ Inc", null))
    val employee3: Employee? = null
    
    println(getEmployeeLocation(employee1)) // Seoul
    println(getEmployeeLocation(employee2)) // Unknown Location
    println(getEmployeeLocation(employee3)) // Unknown Location
}
```
### 4. 컬렉션 처리
```kotlin
fun processItems(items: List<String>?) {
    // items가 null이면 빈 리스트 사용
    val nonNullItems = items ?: emptyList()
    
    // 안전한 처리
    val count = items?.size ?: 0
    val firstItem = items?.firstOrNull() ?: "No items"
    
    println("항목 수: $count")
    println("첫 번째 항목: $firstItem")
    
    // 컬렉션 연산 체이닝
    val processed = items
        ?.filter { it.length > 3 }
        ?.map { it.toUpperCase() }
        ?.joinToString(", ") ?: "No data"
    
    println("처리된 항목: $processed")
}

fun main() {
    processItems(listOf("apple", "banana", "kiwi"))
    processItems(emptyList())
    processItems(null)
}
```
### 5. 함수형 스타일 코드
```kotlin
fun <T, R> T?.runIfNotNull(block: (T) -> R): R? {
    return this?.let(block)
}

fun calculateDiscount(price: Double?, discountPercent: Double?): Double {
    val validPrice = price ?: 0.0
    val validDiscount = discountPercent ?: 0.0
    
    return validPrice * validDiscount / 100.0
}

fun main() {
    val price: Double? = 100.0
    val discount: Double? = 15.0
    
    val discountAmount = price.runIfNotNull { p ->
        discount.runIfNotNull { d ->
            calculateDiscount(p, d)
        } ?: 0.0
    } ?: 0.0
    
    println("할인액: $discountAmount") // 15.0
    
    // 엘비스 연산자를 사용한 간결한 버전
    val simpleDiscount = calculateDiscount(price, discount)
    println("할인액(간결): $simpleDiscount") // 15.0
}
```
## 안전한 호출과 엘비스 연산자 사용 팁

### 1. 체이닝 시 가독성 고려

안전한 호출 체이닝이 너무 길어지면 가독성이 떨어질 수 있습니다. 이런 경우 중간 변수를 사용하거나 `let` 함수를 활용하세요
```kotlin
// 긴 체인
val result = obj?.prop1?.func1()?.prop2?.func2()?.prop3

// 중간 변수 활용
val temp1 = obj?.prop1?.func1()
val temp2 = temp1?.prop2?.func2()
val result = temp2?.prop3

// let 활용
val result = obj?.let {
    it.prop1?.func1()?.let { func1Result ->
        func1Result.prop2?.func2()?.prop3
    }
}
```
### 2. 엘비스 연산자 우선순위 주의

엘비스 연산자는 대부분의 연산자보다 우선순위가 낮습니다. 복잡한 표현식에서는 괄호를 사용하여 명확하게 표현하세요.
```kotlin
// 괄호 없이 - 의도하지 않은 결과 가능
val result = a ?: b + c

// 괄호 사용 - 명확한 의도 표현
val result = a ?: (b + c)
```
### 3. 스마트 캐스트와 함께 사용

안전한 호출 대신 null 검사 후 스마트 캐스트를 활용하면 반복적인 안전한 호출을 줄일 수 있습니다.
```kotlin
// 안전한 호출 반복
val length = person?.name?.length ?: 0

// null 검사 후 스마트 캐스트
if (person != null && person.name != null) {
    val length = person.name.length // 스마트 캐스트
} else {
    val length = 0
}
```
### 4. 안전한 타입 캐스팅 (`as?`)
```kotlin
val obj: Any = "Hello"

// 안전한 타입 캐스팅 + 엘비스 연산자
val length = (obj as? String)?.length ?: -1
```
