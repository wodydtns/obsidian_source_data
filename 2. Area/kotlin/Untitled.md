# Kotlin의 수평적 평가와 수직적 평가

Kotlin에서 컬렉션 처리 시 사용하는 두 가지 주요 평가 방식인 수평적 평가(Horizontal Evaluation)와 수직적 평가(Vertical Evaluation)에 대해 알아보겠습니다. 이 개념들은 주로 컬렉션과 시퀀스의 연산 처리 방식 차이를 설명할 때 사용됩니다.

## 수평적 평가 (Horizontal Evaluation)

수평적 평가는 일반 컬렉션에서 사용하는 방식으로, 각 연산이 전체 컬렉션에 대해 순차적으로 적용됩니다.

### 특징

1. **전체 단계별 처리**: 각 연산이 모든 요소에 대해 완료된 후 다음 연산으로 넘어갑니다.
2. **중간 컬렉션 생성**: 각 연산 단계마다 새로운 컬렉션이 생성됩니다.
3. **즉시 실행(Eager Evaluation)**: 연산이 호출되는 즉시 실행됩니다.

### 작동 방식 예시

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val result = numbers
    .filter { it % 2 == 0 }    // [2, 4] 생성
    .map { it * it }           // [4, 16] 생성
    .toList()                  // 최종 결과
```

수평적 평가 방식에서는:
1. `filter` 연산이 모든 요소에 적용되어 `[2, 4]`라는 새 리스트 생성
2. `map` 연산이 필터링된 리스트의 모든 요소에 적용되어 `[4, 16]`이라는 새 리스트 생성

### 시각적 표현
```kotlin
[1, 2, 3, 4, 5] ----filter----> [2, 4] ----map----> [4, 16]
```
## 수직적 평가 (Vertical Evaluation)

수직적 평가는 시퀀스에서 사용하는 방식으로, 각 요소가 모든 연산을 거친 후 다음 요소로 넘어갑니다.

### 특징

1. **요소별 처리**: 하나의 요소가 모든 연산을 통과한 후 다음 요소로 넘어갑니다.
2. **중간 컬렉션 없음**: 중간 결과를 저장하지 않아 메모리 효율적입니다.
3. **지연 실행(Lazy Evaluation)**: 최종 연산이 호출될 때까지 실제 계산이 지연됩니다.

### 작동 방식 예시
```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val result = numbers.asSequence()
    .filter { it % 2 == 0 }
    .map { it * it }
    .toList()  // 최종 연산 호출 시점에 실제 계산 수행
```
수직적 평가 방식에서는:

1. 요소 `1`에 대해 `filter` 적용 → 조건 불만족으로 다음 요소로
2. 요소 `2`에 대해 `filter` 적용 → 조건 만족 → `map` 적용 → 결과 `4` 수집
3. 요소 `3`에 대해 `filter` 적용 → 조건 불만족으로 다음 요소로
4. 요소 `4`에 대해 `filter` 적용 → 조건 만족 → `map` 적용 → 결과 `16` 수집
5. 요소 `5`에 대해 `filter` 적용 → 조건 불만족으로 다음 요소로
### 시각적 표현
```kotlin
1 --filter--> (제외)
2 --filter--> 2 --map--> 4 --> 결과에 추가
3 --filter--> (제외)
4 --filter--> 4 --map--> 16 --> 결과에 추가
5 --filter--> (제외)
```
## 두 평가 방식의 비교

### 1. 성능 차이

#### 메모리 사용
```kotlin
// 수평적 평가 (컬렉션)
val result1 = (1..1000000)
    .filter { it % 2 == 0 }    // 50만 개의 요소를 가진 새 리스트 생성
    .map { it * it }           // 50만 개의 요소를 가진 새 리스트 생성
    .take(5)                   // 5개 요소를 가진 새 리스트 생성
    .toList()

// 수직적 평가 (시퀀스)
val result2 = (1..1000000).asSequence()
    .filter { it % 2 == 0 }    // 지연 연산
    .map { it * it }           // 지연 연산
    .take(5)                   // 지연 연산
    .toList()                  // 10개의 요소만 처리 후 결과 반환
```
수직적 평가는 중간 컬렉션을 생성하지 않아 메모리 사용이 효율적입니다.
#### 연산 횟수
```kotlin
// 수평적 평가 - 모든 요소에 대해 모든 연산 수행
val numbers = listOf(1, 2, 3, 4, 5)
numbers
    .filter { println("Filtering $it"); it % 2 == 0 }
    .map { println("Mapping $it"); it * it }
    .first()  // 첫 번째 요소만 필요해도 모든 요소 처리

// 출력:
// Filtering 1
// Filtering 2
// Filtering 3
// Filtering 4
// Filtering 5
// Mapping 2
// Mapping 4

// 수직적 평가 - 필요한 요소만 처리
val numbers = listOf(1, 2, 3, 4, 5)
numbers.asSequence()
    .filter { println("Filtering $it"); it % 2 == 0 }
    .map { println("Mapping $it"); it * it }
    .first()  // 첫 번째 요소 찾으면 중단

// 출력:
// Filtering 1
// Filtering 2
// Mapping 2
```

수직적 평가는 필요한 연산만 수행하므로 특히 `first()`, `find()`, `take(n)` 등의 연산에서 효율적입니다.

### 2. 적합한 사용 사례

#### 수평적 평가가 유리한 경우

1. **작은 컬렉션**: 요소 수가 적은 경우 오버헤드가 적습니다.
2. **여러 번 순회**: 결과를 여러 번 사용할 경우 미리 계산해두는 것이 효율적입니다.
3. **복잡한 연산 최적화**: 컴파일러가 전체 연산을 한 번에 최적화할 수 있습니다.
```kotlin
// 작은 컬렉션에서는 일반 컬렉션 연산이 더 효율적일 수 있음
val smallList = listOf(1, 2, 3, 4, 5)
val result = smallList.filter { it % 2 == 0 }.map { it * it }
```
#### 수직적 평가가 유리한 경우

1. **대용량 데이터**: 메모리 효율성이 중요한 경우
2. **무한 시퀀스**: 무한한 요소를 다룰 때
3. **조기 종료**: 모든 요소를 처리하지 않고 조건에 맞는 일부만 필요한 경우
4. **I/O 작업**: 파일이나 네트워크에서 데이터를 읽을 때
```kotlin
// 파일 처리 예제 - 수직적 평가 사용
File("large_log.txt").useLines { lines ->
    lines.asSequence()
        .filter { it.contains("ERROR") }
        .take(10)
        .forEach(::println)
}
```
## 실제 예제로 보는 두 평가 방식의 차이

### 예제 1: 연산 실행 순서 확인
```kotlin
fun main() {
    println("컬렉션 (수평적 평가):")
    listOf(1, 2, 3, 4, 5)
        .filter { 
            println("필터링: $it")
            it % 2 == 0 
        }
        .map { 
            println("매핑: $it")
            it * it 
        }
        .forEach { 
            println("최종 결과: $it")
        }
    
    println("\n시퀀스 (수직적 평가):")
    listOf(1, 2, 3, 4, 5)
        .asSequence()
        .filter { 
            println("필터링: $it")
            it % 2 == 0 
        }
        .map { 
            println("매핑: $it")
            it * it 
        }
        .forEach { 
            println("최종 결과: $it")
        }
}
```
출력 결과
```kotlin
컬렉션 (수평적 평가):
필터링: 1
필터링: 2
필터링: 3
필터링: 4
필터링: 5
매핑: 2
매핑: 4
최종 결과: 4
최종 결과: 16

시퀀스 (수직적 평가):
필터링: 1
필터링: 2
매핑: 2
최종 결과: 4
필터링: 3
필터링: 4
매핑: 4
최종 결과: 16
필터링: 5
```
### 예제 2: 성능 차이 측정
```kotlin
fun main() {
    val largeList = (1..1000000).toList()
    
    // 시간 측정 - 수평적 평가
    val startTime1 = System.currentTimeMillis()
    val result1 = largeList
        .filter { it % 2 == 0 }
        .map { it * it }
        .take(5)
        .toList()
    val endTime1 = System.currentTimeMillis()
    
    // 시간 측정 - 수직적 평가
    val startTime2 = System.currentTimeMillis()
    val result2 = largeList.asSequence()
        .filter { it % 2 == 0 }
        .map { it * it }
        .take(5)
        .toList()
    val endTime2 = System.currentTimeMillis()
    
    println("결과: $result1")
    println("수평적 평가 시간: ${endTime1 - startTime1}ms")
    println("수직적 평가 시간: ${endTime2 - startTime2}ms")
}
```
## 결론

- **수평적 평가(컬렉션)**는 각 연산이 전체 컬렉션에 적용되고 중간 결과가 저장됩니다. 작은 데이터셋이나 결과를 여러 번 사용할 때 적합합니다.
    
- **수직적 평가(시퀀스)**는 각 요소가 모든 연산을 거친 후 다음 요소로 넘어가며, 중간 결과를 저장하지 않습니다. 대용량 데이터, 무한 시퀀스, 조기 종료가 필요한 경우에 적합합니다.
    

적절한 평가 방식을 선택하면 코드의 성능과 메모리 효율성을 크게 향상시킬 수 있습니다. 일반적으로 대용량 데이터를 처리하거나 모든 요소를 처리할 필요가 없는 경우에는 시퀀스(수직적 평가)를 사용하는 것이 좋습니다.