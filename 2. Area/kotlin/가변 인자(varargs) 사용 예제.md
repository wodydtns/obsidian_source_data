```kotlin
// 가변 인자(varargs) 사용 예제
fun main() {
    // 1. 가변 인자를 사용하는 함수 호출
    printAll("Hello", "Kotlin", "Varargs", "Example")
    
    // 2. 배열을 가변 인자로 전달 (스프레드 연산자 * 사용)
    val array = arrayOf("배열", "요소", "전달")
    printAll(*array)
    
    // 3. 가변 인자와 일반 인자 함께 사용
    printWithPrefix("접두어: ", "첫 번째", "두 번째", "세 번째")
    
    // 4. 가변 인자로 합계 계산
    val sum = sumNumbers(1, 2, 3, 4, 5)
    println("합계: $sum")
    
    // 5. 타입이 다른 가변 인자 처리
    printAnyValues(1, "문자열", true, 3.14)
    
    // 6. 가변 인자를 사용한 리스트 생성
    val list = createList("사과", "바나나", "오렌지")
    println("생성된 리스트: $list")
    
    // 7. 가변 인자를 사용한 맵 생성
    val map = createMap("key1" to "value1", "key2" to "value2", "key3" to "value3")
    println("생성된 맵: $map")
}

// 기본적인 가변 인자 함수
fun printAll(vararg messages: String) {
    for (message in messages) {
        println(message)
    }
    println("총 ${messages.size}개의 메시지 출력됨")
}

// 일반 매개변수와 가변 인자를 함께 사용
fun printWithPrefix(prefix: String, vararg words: String) {
    for (word in words) {
        println("$prefix$word")
    }
}

// 숫자 가변 인자의 합계 계산
fun sumNumbers(vararg numbers: Int): Int {
    var sum = 0
    for (number in numbers) {
        sum += number
    }
    return sum
}

// Any 타입을 사용한 다양한 타입의 가변 인자
fun printAnyValues(vararg values: Any) {
    for (value in values) {
        println("값: $value, 타입: ${value::class.simpleName}")
    }
}

// 가변 인자를 사용한 리스트 생성 함수
fun <T> createList(vararg items: T): List<T> {
    return items.toList()
}

// 가변 인자를 사용한 맵 생성 함수
fun <K, V> createMap(vararg pairs: Pair<K, V>): Map<K, V> {
    return mapOf(*pairs)
}

// 가변 인자를 다른 함수에 전달
fun forwardVarargs(vararg strings: String) {
    // 가변 인자를 다른 함수로 전달할 때 스프레드 연산자(*) 사용
    printAll(*strings)
}

// 제네릭 타입과 가변 인자 함께 사용
class GenericVarargs {
    fun <T> printGenericItems(vararg items: T) {
        items.forEach { println("제네릭 아이템: $it") }
    }
}

// 확장 함수에서 가변 인자 사용
fun String.format(vararg args: Any): String {
    var result = this
    args.forEachIndexed { index, arg ->
        result = result.replace("{$index}", arg.toString())
    }
    return result
}

// 가변 인자를 사용하는 확장 함수 예제
fun main() {
    // 확장 함수 사용
    val template = "이름: {0}, 나이: {1}, 직업: {2}"
    val formatted = template.format("홍길동", 30, "개발자")
    println(formatted)
    
    // 제네릭 가변 인자 사용
    val generic = GenericVarargs()
    generic.printGenericItems(1, "문자열", true, 3.14)
    
    // 가변 인자 전달
    forwardVarargs("전달된", "가변", "인자", "예제")
}

```