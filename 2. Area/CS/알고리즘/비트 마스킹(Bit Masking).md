**비트 마스킹(Bit Masking)**은 이진수를 이용하여 비트 단위로 데이터를 표현하고 조작하는 기법입니다. 주로 **비트 연산자**를 사용하여 효율적으로 데이터를 처리하며, 메모리 사용량을 최소화하고 실행 속도를 향상시킬 수 있습니다.

## 개념

- **비트(Bit)**: 컴퓨터에서 가장 작은 단위로, 0 또는 1의 값을 가집니다.
- **마스크(Mask)**: 특정 비트를 선택하거나 변경하기 위해 사용되는 비트 패턴입니다.
- **비트 연산자**: 비트 단위로 연산을 수행하는 연산자입니다.

## 주요 비트 연산자

|연산자|설명|예시 (`a = 5`, `b = 3`)|
|---|---|---|
|`&`|비트 AND 연산|`a & b` → `1`|
|`|`|비트 OR 연산|
|`^`|비트 XOR 연산|`a ^ b` → `6`|
|`~`|비트 NOT 연산|`~a` → `-6`|
|`<<`|비트 왼쪽 시프트|`a << 1` → `10`|
|`>>`|비트 오른쪽 시프트|`a >> 1` → `2`|

## 비트 마스킹의 활용 분야

- **부분 집합 생성**: 집합의 모든 부분 집합을 비트 표현으로 생성.
- **상태 압축(State Compression)**: 여러 상태 정보를 하나의 정수로 표현.
- **그래프 탐색 최적화**: 방문 여부를 비트로 표현하여 메모리 절약.
- **권한 관리**: 접근 권한을 비트로 표현하여 효율적으로 관리.
- **효율적인 데이터 저장**: 불리언 배열 등을 비트로 표현하여 메모리 사용량 감소.

## 자바로 구현하기

### 1. 부분 집합 생성

n개의 원소가 있을 때, 모든 부분 집합의 수는 2n2^n2n개입니다. 비트 마스킹을 이용하면 부분 집합을 효율적으로 생성할 수 있습니다.

#### 코드 구현
```Java
public class SubsetGenerator {
    public static void main(String[] args) {
        int[] set = {1, 2, 3};
        int n = set.length;

        // 부분 집합의 수는 2^n개
        int subsetCount = 1 << n; // 2^n

        System.out.println("모든 부분 집합:");
        for (int i = 0; i < subsetCount; i++) {
            System.out.print("{ ");
            for (int j = 0; j < n; j++) {
                // i의 j번째 비트가 1인지 확인
                if ((i & (1 << j)) != 0) {
                    System.out.print(set[j] + " ");
                }
            }
            System.out.println("}");
        }
    }
}

```
#### 코드 설명

- **비트 마스크 생성**: `0`부터 `2^n - 1`까지의 숫자를 이용하여 각 부분 집합을 표현합니다.
- **비트 검사**: `(i & (1 << j))`를 통해 `i`의 `j`번째 비트가 `1`인지 확인합니다.
- **부분 집합 생성**: 비트가 `1`인 경우 해당 원소를 부분 집합에 포함합니다.

### 2. 비트마스크를 이용한 방문 체크 (예: 순열 생성)

비트 마스킹을 사용하여 방문 여부를 관리하면 배열 대신 정수형 변수를 사용할 수 있어 메모리와 속도 면에서 효율적입니다.

#### 코드 구현
```Java
import java.util.ArrayList;
import java.util.List;

public class Permutation {
    static int n;
    static int[] nums;
    static List<List<Integer>> result = new ArrayList<>();

    public static void main(String[] args) {
        nums = new int[]{1, 2, 3};
        n = nums.length;

        permute(0, new ArrayList<>(), 0);

        System.out.println("모든 순열:");
        for (List<Integer> perm : result) {
            System.out.println(perm);
        }
    }

    public static void permute(int depth, List<Integer> current, int visited) {
        if (depth == n) {
            result.add(new ArrayList<>(current));
            return;
        }

        for (int i = 0; i < n; i++) {
            if ((visited & (1 << i)) == 0) {
                current.add(nums[i]);
                permute(depth + 1, current, visited | (1 << i));
                current.remove(current.size() - 1);
            }
        }
    }
}

```

#### 코드 설명

- **방문 체크**: `int visited`를 비트마스크로 사용하여 원소의 방문 여부를 체크합니다.
- **비트 연산**:
    - 방문 여부 확인: `(visited & (1 << i)) == 0`
    - 방문 처리: `visited | (1 << i)`
- **백트래킹**: 재귀 호출 후에는 원상 복귀를 위해 마지막 원소를 제거합니다.

### 3. 그래프에서의 비트 마스킹 (예: 외판원 순회 문제)

비트 마스킹을 사용하여 그래프 탐색에서 방문 상태를 표현하면 상태 공간을 줄일 수 있습니다.

#### 문제 설명

- N개의 도시가 있고, 한 도시에서 다른 도시로 이동하는 비용이 주어집니다.
- 모든 도시를 한 번씩 방문하고 시작 도시로 돌아오는 최소 비용을 구하는 문제입니다.

#### 코드 구현 (간단한 형태)
```Java
public class TSP {
    static final int INF = 100000000;
    static int N;
    static int[][] dist;
    static int[][] dp;

    public static void main(String[] args) {
        N = 4;
        dist = new int[][]{
            {0, 10, 15, 20},
            {10, 0, 35, 25},
            {15, 35, 0, 30},
            {20, 25, 30, 0}
        };
        dp = new int[N][1 << N];

        int result = tsp(0, 1);
        System.out.println("최소 비용: " + result);
    }

    public static int tsp(int current, int visited) {
        if (visited == (1 << N) - 1) {
            // 모든 도시를 방문한 경우
            return dist[current][0] != 0 ? dist[current][0] : INF;
        }

        if (dp[current][visited] != 0) {
            return dp[current][visited];
        }

        int minCost = INF;
        for (int i = 0; i < N; i++) {
            // 방문하지 않은 도시이고, 현재 도시에서 갈 수 있는 경우
            if ((visited & (1 << i)) == 0 && dist[current][i] != 0) {
                int cost = dist[current][i] + tsp(i, visited | (1 << i));
                minCost = Math.min(minCost, cost);
            }
        }
        dp[current][visited] = minCost;
        return minCost;
    }
}

```

#### 코드 설명

- **`dp[current][visited]`**: 현재 도시와 방문 상태를 기반으로 최소 비용을 메모이제이션합니다.
- **방문 상태 표현**: `int visited`를 사용하여 각 도시의 방문 여부를 비트마스크로 관리합니다.
- **모든 도시 방문 확인**: `visited == (1 << N) - 1`이면 모든 도시를 방문한 상태입니다.

## 주요 비트 연산 테크닉

### 1. k번째 비트 확인
```Java
boolean isBitSet(int num, int k) {
    return (num & (1 << k)) != 0;
}

```

### 2. k번째 비트 설정
```Java
int setBit(int num, int k) {
    return num | (1 << k);
}

```

### 3. k번째 비트 해제
```Java
int clearBit(int num, int k) {
    return num & ~(1 << k);
}

```

### 4. 최하위 1비트 찾기
```Java
int lowestBit(int num) {
    return num & -num;
}

```

### 5. 비트 개수 세기 (1의 개수)
```Java
int countBits(int num) {
    return Integer.bitCount(num);
}

```

## 비트 마스킹 사용 시 고려사항

- **정수형 범위**: 비트 마스킹 시 사용하는 정수형의 비트 수에 제한이 있으므로, 큰 n에 대해선 다른 방법을 고려해야 합니다.
    - 자바에서 `int`는 32비트, `long`은 64비트입니다.
- **오버플로우 주의**: 비트 연산 중 오버플로우가 발생하지 않도록 주의해야 합니다.
- **가독성**: 비트 연산은 코드의 가독성을 떨어뜨릴 수 있으므로, 주석이나 별도의 함수로 명확히 표현하는 것이 좋습니다.

## 비트 마스킹의 장점과 단점

### 장점

- **메모리 효율성**: 불리언 배열 등을 정수 하나로 표현하여 메모리를 절약할 수 있습니다.
- **속도 향상**: 비트 연산은 일반적으로 매우 빠르므로 알고리즘의 성능을 향상시킬 수 있습니다.
- **상태 표현 간결성**: 여러 상태를 하나의 정수로 표현하여 코드가 간결해집니다.

### 단점

- **이해하기 어려움**: 비트 연산은 직관적이지 않아 이해하기 어려울 수 있습니다.
- **확장성 제한**: 비트 수의 한계로 인해 표현할 수 있는 상태의 수가 제한됩니다.
- **디버깅 어려움**: 비트 단위의 버그는 찾기 어려울 수 있습니다.

## 결론

비트 마스킹은 알고리즘 문제 해결에서 강력한 도구로, 특히 상태를 효율적으로 관리하거나 부분 집합을 생성하는 데 유용합니다. 비트 연산에 익숙해지면 메모리와 시간을 절약하면서도 복잡한 문제를 해결할 수 있습니다. 하지만 코드의 가독성과 정수형 범위의 제한 등을 고려하여 적절히 활용하는 것이 중요합니다.