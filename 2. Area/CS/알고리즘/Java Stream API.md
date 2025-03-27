# Java Stream API

## 목차
1. [[#Stream 이란?]]
2. [[#Stream 생성 방법]]
3. [[#주요 Stream 연산]]
4. [[#실전 예제]]

## Stream 이란?
Stream API는 Java 8에서 도입된 기능으로, 컬렉션의 요소들을 함수형 프로그래밍 방식으로 처리할 수 있게 해주는 기능입니다.

### 특징
- 원본 데이터를 변경하지 않음
- 일회용 (한 번 사용한 스트림은 재사용 불가)
- 내부 반복으로 작업을 처리
- 지연 연산 지원

## Stream 생성 방법

### 컬렉션으로부터 생성
```java
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> stream = list.stream();

// 병렬 스트림 생성
Stream<String> parallelStream = list.parallelStream();
```

### 배열로부터 생성
```java
String[] arr = {"a", "b", "c"};
Stream<String> stream = Arrays.stream(arr);
```

### Stream.of() 사용
```java
Stream<String> stream = Stream.of("a", "b", "c");
```

### 무한 스트림 생성
```java
// iterate를 이용한 무한 스트림
Stream<Integer> infiniteStream = Stream.iterate(0, n -> n + 2);

// generate를 이용한 무한 스트림
Stream<Double> randomStream = Stream.generate(Math::random);
```

## 주요 Stream 연산

### 중간 연산 (Intermediate Operations)
중간 연산은 스트림을 반환하므로 체이닝이 가능합니다.

| 연산       | 설명               | 예제                                 |
| -------- | ---------------- | ---------------------------------- |
| filter   | 조건에 맞는 요소 필터링    | `stream.filter(n -> n > 0)`        |
| map      | 요소를 변환           | `stream.map(String::toUpperCase)`  |
| flatMap  | 중첩 구조를 단일 구조로 변환 | `stream.flatMap(Arrays::stream)`   |
| distinct | 중복 제거            | `stream.distinct()`                |
| sorted   | 정렬               | `stream.sorted()`                  |
| peek     | 요소를 순회하며 처리      | `stream.peek(System.out::println)` |
| limit    | 요소 개수 제한         | `stream.limit(5)`                  |
| skip     | 처음 n개 요소 건너뛰기    | `stream.skip(3)`                   |
| boxed | 기본 타입 스트림을 래퍼 클래스 스트림으로 변환 | `intStream.boxed()` |
|          |                  |                                    |

### 최종 연산 (Terminal Operations)
최종 연산은 스트림을 소비하고 결과를 반환합니다.

| 연산 | 설명 | 예제 |
|------|------|------|
| forEach | 각 요소에 대해 작업 수행 | `stream.forEach(System.out::println)` |
| collect | 요소를 수집하여 다른 형태로 변환 | `stream.collect(Collectors.toList())` |
| reduce | 요소를 하나로 줄임 | `stream.reduce(0, Integer::sum)` |
| count | 요소 개수 반환 | `stream.count()` |
| anyMatch | 조건을 만족하는 요소가 하나라도 있는지 확인 | `stream.anyMatch(n -> n > 0)` |
| allMatch | 모든 요소가 조건을 만족하는지 확인 | `stream.allMatch(n -> n > 0)` |
| noneMatch | 모든 요소가 조건을 만족하지 않는지 확인 | `stream.noneMatch(n -> n > 0)` |
| findFirst | 첫 번째 요소 반환 | `stream.findFirst()` |
| findAny | 아무 요소나 반환 | `stream.findAny()` |
| min | 최솟값 반환 | `stream.min(Comparator.naturalOrder())` |
| max | 최댓값 반환 | `stream.max(Comparator.naturalOrder())` |

## 실전 예제

### 1. 숫자 리스트 처리
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// 짝수만 필터링하여 제곱한 후 평균 구하기
double average = numbers.stream()
    .filter(n -> n % 2 == 0)
    .map(n -> n * n)
    .mapToInt(Integer::intValue)
    .average()
    .orElse(0.0);
```

### 2. 문자열 리스트 처리
```java
List<String> words = Arrays.asList("Hello", "World", "Java", "Stream");

// 길이가 5 이상인 단어를 대문자로 변환하여 리스트로 수집
List<String> longWords = words.stream()
    .filter(w -> w.length() >= 5)
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```

### 3. 객체 리스트 처리
```java
class Person {
    String name;
    int age;
    // 생성자, getter, setter 생략
}

List<Person> people = // 사람 목록

// 나이가 20 이상인 사람들의 이름을 알파벳 순으로 정렬
List<String> adultNames = people.stream()
    .filter(p -> p.getAge() >= 20)
    .map(Person::getName)
    .sorted()
    .collect(Collectors.toList());
```

### 4. Collectors 활용
```java
// 그룹화
Map<Integer, List<Person>> peopleByAge = people.stream()
    .collect(Collectors.groupingBy(Person::getAge));

// 평균 계산
double averageAge = people.stream()
    .collect(Collectors.averagingInt(Person::getAge));

// 문자열 결합
String names = people.stream()
    .map(Person::getName)
    .collect(Collectors.joining(", "));
```

## 참고 사항
- Stream은 데이터의 흐름을 표현하는 객체입니다.
- 한 번 사용한 스트림은 재사용할 수 없습니다.
- 병렬 처리가 필요한 경우 `parallelStream()`을 사용합니다.
- 대용량 데이터 처리 시 Stream API를 활용하면 코드가 간결해지고 가독성이 향상됩니다.

#java #stream #functional-programming #java8