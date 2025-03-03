```kotlin
// Set 예제
fun main() {
    // 읽기 전용 Set 생성
    val readOnlySet = setOf("사과", "바나나", "오렌지", "사과")
    println("읽기 전용 Set: $readOnlySet") // 중복 요소 "사과"는 한 번만 포함됨
    
    // 빈 Set 생성
    val emptySet = emptySet<String>()
    println("빈 Set: $emptySet")
    
    // Set 기본 연산
    println("Set 크기: ${readOnlySet.size}")
    println("'바나나' 포함 여부: ${"바나나" in readOnlySet}")
    println("'포도' 포함 여부: ${"포도" in readOnlySet}")
    
    // Set의 반복
    println("Set 요소 출력:")
    for (item in readOnlySet) {
        println("- $item")
    }
    
    // 가변 Set (MutableSet) 생성
    val mutableSet = mutableSetOf("사과", "바나나", "오렌지")
    println("\n가변 Set: $mutableSet")
    
    // 요소 추가
    mutableSet.add("포도")
    println("'포도' 추가 후: $mutableSet")
    
    // 중복 요소 추가 시도 (무시됨)
    val wasAdded = mutableSet.add("사과")
    println("'사과' 추가 성공 여부: $wasAdded")
    println("중복 추가 시도 후: $mutableSet")
    
    // 요소 제거
    mutableSet.remove("바나나")
    println("'바나나' 제거 후: $mutableSet")
    
    // 여러 요소 추가
    mutableSet.addAll(listOf("키위", "망고"))
    println("여러 요소 추가 후: $mutableSet")
    
    // Set 비우기
    mutableSet.clear()
    println("비운 후: $mutableSet")
    
    // LinkedHashSet 사용 (삽입 순서 유지)
    val linkedHashSet = linkedSetOf("C", "B", "A", "D")
    println("\nLinkedHashSet (삽입 순서 유지): $linkedHashSet")
    
    // HashSet 사용 (순서 보장 안됨)
    val hashSet = hashSetOf("C", "B", "A", "D")
    println("HashSet (순서 무관): $hashSet")
    
    // SortedSet/TreeSet 사용 (정렬된 순서)
    val sortedSet = sortedSetOf("C", "B", "A", "D")
    println("SortedSet (정렬된 순서): $sortedSet")
    
    // Set 연산
    val set1 = setOf(1, 2, 3, 4)
    val set2 = setOf(3, 4, 5, 6)
    
    println("\nset1: $set1")
    println("set2: $set2")
    println("합집합: ${set1.union(set2)}")
    println("교집합: ${set1.intersect(set2)}")
    println("차집합(set1 - set2): ${set1.subtract(set2)}")
    println("대칭 차집합: ${set1.union(set2).subtract(set1.intersect(set2))}")
}

// Map 예제
fun main() {
    // 읽기 전용 Map 생성
    val readOnlyMap = mapOf(
        "사과" to 100,
        "바나나" to 150,
        "오렌지" to 200
    )
    println("읽기 전용 Map: $readOnlyMap")
    
    // 빈 Map 생성
    val emptyMap = emptyMap<String, Int>()
    println("빈 Map: $emptyMap")
    
    // Map 요소 접근
    println("\n'사과'의 가격: ${readOnlyMap["사과"]}")
    println("'포도'의 가격: ${readOnlyMap["포도"]}") // null 반환
    println("'포도'의 가격 (기본값 사용): ${readOnlyMap.getOrDefault("포도", 0)}")
    
    // Map 기본 연산
    println("\nMap 크기: ${readOnlyMap.size}")
    println("키 '바나나' 포함 여부: ${"바나나" in readOnlyMap}")
    println("값 200 포함 여부: ${200 in readOnlyMap.values}")
    
    // Map의 키, 값, 엔트리 접근
    println("\n모든 키: ${readOnlyMap.keys}")
    println("모든 값: ${readOnlyMap.values}")
    println("모든 엔트리: ${readOnlyMap.entries}")
    
    // Map 반복
    println("\nMap 요소 출력:")
    for ((key, value) in readOnlyMap) {
        println("$key: $value")
    }
    
    // 가변 Map (MutableMap) 생성
    val mutableMap = mutableMapOf(
        "사과" to 100,
        "바나나" to 150,
        "오렌지" to 200
    )
    println("\n가변 Map: $mutableMap")
    
    // 요소 추가 및 수정
    mutableMap["포도"] = 250 // 새 항목 추가
    println("'포도' 추가 후: $mutableMap")
    
    mutableMap["사과"] = 120 // 기존 항목 수정
    println("'사과' 가격 수정 후: $mutableMap")
    
    // 요소 제거
    mutableMap.remove("바나나")
    println("'바나나' 제거 후: $mutableMap")
    
    // 조건부 제거
    mutableMap.remove("오렌지", 150) // 값이 일치하지 않아 제거되지 않음
    println("조건부 제거 시도 후: $mutableMap")
    
    mutableMap.remove("오렌지", 200) // 값이 일치하여 제거됨
    println("조건부 제거 성공 후: $mutableMap")
    
    // 여러 항목 추가
    mutableMap.putAll(mapOf("키위" to 180, "망고" to 300))
    println("여러 항목 추가 후: $mutableMap")
    
    // Map 비우기
    mutableMap.clear()
    println("비운 후: $mutableMap")
    
    // LinkedHashMap 사용 (삽입 순서 유지)
    val linkedHashMap = linkedMapOf("C" to 3, "B" to 2, "A" to 1, "D" to 4)
    println("\nLinkedHashMap (삽입 순서 유지): $linkedHashMap")
    
    // HashMap 사용 (순서 보장 안됨)
    val hashMap = hashMapOf("C" to 3, "B" to 2, "A" to 1, "D" to 4)
    println("HashMap (순서 무관): $hashMap")
    
    // SortedMap/TreeMap 사용 (키 기준 정렬)
    val sortedMap = sortedMapOf("C" to 3, "B" to 2, "A" to 1, "D" to 4)
    println("SortedMap (키 기준 정렬): $sortedMap")
    
    // Map 변환 및 필터링
    val prices = mapOf("사과" to 100, "바나나" to 150, "오렌지" to 200, "포도" to 250)
    
    // 키 필터링
    val expensiveItems = prices.filterKeys { it.length > 2 }
    println("\n이름이 긴 과일: $expensiveItems")
    
    // 값 필터링
    val expensivePrices = prices.filterValues { it > 150 }
    println("비싼 과일: $expensivePrices")
    
    // 키와 값 모두 필터링
    val filteredMap = prices.filter { (key, value) -> key.startsWith("오") && value > 150 }
    println("'오'로 시작하고 비싼 과일: $filteredMap")
    
    // Map 변환
    val discountedPrices = prices.mapValues { (_, value) -> value * 0.9 }
    println("10% 할인된 가격: $discountedPrices")
}

```