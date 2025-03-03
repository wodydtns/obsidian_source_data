```kotlin
// 읽기 전용 List (Immutable List)
fun main() {
    // 읽기 전용 List 생성 방법들
    
    // 1. listOf 함수 사용
    val readOnlyList1 = listOf("사과", "바나나", "오렌지")
    
    // 2. emptyList 함수 사용 (빈 리스트)
    val emptyReadOnlyList = emptyList<String>()
    
    // 3. listOfNotNull 함수 사용 (null이 아닌 요소만 포함)
    val readOnlyList2 = listOfNotNull("사과", null, "바나나", null, "오렌지")
    
    // 읽기 전용 List 사용 예
    println("첫 번째 리스트: $readOnlyList1")
    println("첫 번째 요소: ${readOnlyList1[0]}")
    println("리스트 크기: ${readOnlyList1.size}")
    println("바나나 포함 여부: ${"바나나" in readOnlyList1}")
    
    // 아래 코드는 컴파일 에러 발생 - 읽기 전용 List는 변경 불가
    // readOnlyList1.add("포도")     // 컴파일 에러
    // readOnlyList1[0] = "키위"     // 컴파일 에러
    // readOnlyList1.removeAt(0)    // 컴파일 에러
    
    // List의 변환 및 조작 (새로운 List 반환)
    val filteredList = readOnlyList1.filter { it.length > 2 }
    val mappedList = readOnlyList1.map { it.uppercase() }
    
    println("필터링된 리스트: $filteredList")
    println("대문자로 변환된 리스트: $mappedList")
}

// 가변 List (Mutable List)
fun main() {
    // 가변 List 생성 방법들
    
    // 1. mutableListOf 함수 사용
    val mutableList1 = mutableListOf("사과", "바나나", "오렌지")
    
    // 2. ArrayList 생성자 사용
    val mutableList2 = ArrayList<String>()
    mutableList2.add("사과")
    mutableList2.add("바나나")
    
    // 3. emptyMutableList 함수 사용 (빈 가변 리스트)
    val emptyMutableList = mutableListOf<String>()
    
    // 4. arrayListOf 함수 사용
    val mutableList3 = arrayListOf("사과", "바나나", "오렌지")
    
    // 가변 List 사용 예
    println("가변 리스트: $mutableList1")
    
    // 요소 추가
    mutableList1.add("포도")
    println("요소 추가 후: $mutableList1")
    
    // 특정 위치에 요소 추가
    mutableList1.add(1, "키위")
    println("키위 추가 후: $mutableList1")
    
    // 요소 변경
    mutableList1[0] = "청사과"
    println("첫 요소 변경 후: $mutableList1")
    
    // 요소 제거
    mutableList1.removeAt(2)
    println("요소 제거 후: $mutableList1")
    
    // 특정 요소 제거
    mutableList1.remove("포도")
    println("포도 제거 후: $mutableList1")
    
    // 리스트 비우기
    mutableList1.clear()
    println("리스트 비운 후: $mutableList1")
    println("리스트가 비었나요? ${mutableList1.isEmpty()}")
    
    // 다시 요소 추가
    mutableList1.addAll(listOf("딸기", "블루베리", "라즈베리"))
    println("요소 다시 추가 후: $mutableList1")
    
    // 정렬
    mutableList1.sort()
    println("정렬 후: $mutableList1")
    
    // 역순 정렬
    mutableList1.sortDescending()
    println("역순 정렬 후: $mutableList1")
}

// 읽기 전용 List를 가변 List로 변환
fun main() {
    // 읽기 전용 List 생성
    val readOnlyList = listOf("사과", "바나나", "오렌지")
    
    // 읽기 전용 List를 가변 List로 변환
    val mutableList = readOnlyList.toMutableList()
    
    // 이제 mutableList는 수정 가능
    mutableList.add("포도")
    mutableList[0] = "청사과"
    
    println("원본 읽기 전용 리스트: $readOnlyList")
    println("변환된 가변 리스트: $mutableList")
}

// 가변 List를 읽기 전용으로 사용하기
fun main() {
    // 가변 List 생성
    val mutableList = mutableListOf("사과", "바나나", "오렌지")
    
    // 가변 List를 읽기 전용 List로 참조
    val readOnlyView: List<String> = mutableList
    
    // readOnlyView를 통해서는 수정 불가능
    // readOnlyView.add("포도")  // 컴파일 에러
    
    // 하지만 원본 mutableList를 통해 수정하면 readOnlyView에도 반영됨
    mutableList.add("포도")
    
    println("가변 리스트: $mutableList")
    println("읽기 전용 뷰: $readOnlyView")  // 포도가 포함되어 있음
}
```

