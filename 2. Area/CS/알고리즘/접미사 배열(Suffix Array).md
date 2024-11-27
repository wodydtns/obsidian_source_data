**접미사 배열(Suffix Array)**와 **접미사 트리(Suffix Tree)**는 문자열 알고리즘에서 중요한 자료구조로, 문자열의 모든 접미사를 효율적으로 저장하고 검색하는 데 사용됩니다. 이들은 문자열 매칭, 중복 서브스트링 검색, 사전식 순서 정렬 등 다양한 응용 분야에서 활용됩니다.

## 접미사 배열(Suffix Array)

### 개념

- **접미사(Suffix)**: 문자열의 시작 위치부터 끝까지의 부분 문자열입니다.
- **접미사 배열**: 주어진 문자열의 모든 접미사를 사전식(Lexicographical)으로 정렬한 배열입니다.

### 특징

- **공간 효율성**: 접미사 트리에 비해 메모리 사용량이 적습니다.
- **정렬된 구조**: 이진 탐색을 통해 문자열 검색을 효율적으로 수행할 수 있습니다.
- **구현의 단순성**: 비교적 구현이 간단하며, 다양한 알고리즘으로 구축 가능합니다.

### 접미사 배열 구축 방법

1. **단순한 방법**: 모든 접미사를 생성하여 배열에 저장한 후 정렬합니다.
    - 시간 복잡도: O(n² log n)
2. **효율적인 방법**: 접미사 배열 구축을 위한 알고리즘을 사용합니다.
    - 예: SA-IS 알고리즘 (O(n)), Suffix Automaton 등.

### 자바로 구현하기

#### 단순한 방법으로 접미사 배열 생성
```Java
import java.util.Arrays;

public class SuffixArraySimple {
    public static void main(String[] args) {
        String text = "banana";
        int n = text.length();

        // 모든 접미사를 저장할 배열
        String[] suffixes = new String[n];

        // 접미사 생성
        for (int i = 0; i < n; i++) {
            suffixes[i] = text.substring(i);
        }

        // 사전식 정렬
        Arrays.sort(suffixes);

        // 결과 출력
        System.out.println("접미사 배열:");
        for (String suffix : suffixes) {
            System.out.println(suffix);
        }
    }
}

```
#### 코드 설명

- **접미사 생성**: 원본 문자열의 모든 접미사를 생성하여 배열에 저장합니다.
- **정렬**: `Arrays.sort()` 메서드를 사용하여 접미사를 사전식으로 정렬합니다.
- **출력**: 정렬된 접미사 배열을 출력합니다.

### 접미사 배열의 응용

- **문자열 검색**: 특정 패턴이 문자열 내에 존재하는지 확인.
- **중복 서브스트링 검색**: 가장 긴 중복 부분 문자열 찾기.
- **사전식 순서 처리**: 문자열의 순서를 처리하는 문제.
- **데이터 압축**: 버로스-휠러 변환(Burrows-Wheeler Transform)에 사용.