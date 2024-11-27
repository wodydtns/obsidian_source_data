**슬라이딩 윈도우(Sliding Window)** 알고리즘은 배열이나 리스트에서 일정한 범위(윈도우)의 데이터를 효율적으로 처리하기 위한 기법입니다. 이 방법은 주로 연속적인 데이터 집합에서 부분 합, 평균, 최대/최소 값 등을 구할 때 사용됩니다.

## 개념

- **윈도우(Window)**: 배열이나 리스트에서 일정한 범위를 나타내는 구간입니다.
- **슬라이딩(Sliding)**: 윈도우를 시작부터 끝까지 이동시키며 필요한 연산을 수행합니다.
- **고정 크기 윈도우**: 윈도우의 크기가 일정한 경우.
- **가변 크기 윈도우**: 윈도우의 크기가 변하는 경우.

## 특징

- **효율성**: 이전 윈도우의 결과를 활용하여 다음 윈도우의 결과를 빠르게 계산할 수 있습니다.
- **시간 복잡도 감소**: 전체 데이터를 매번 재계산하지 않고 윈도우 내에서 필요한 연산만 수행하므로 시간 복잡도를 줄일 수 있습니다.

## 적용 분야

- **부분 배열 또는 부분 문자열 처리**: 합, 평균, 최대/최소 값 등 계산.
- **패턴 찾기**: 문자열 내에서 특정 패턴이나 조건을 만족하는 부분 문자열 찾기.
- **스트림 데이터 처리**: 실시간으로 들어오는 데이터에서 일정한 구간의 정보를 처리.

## 알고리즘 과정

1. **윈도우 초기화**: 시작점과 크기를 설정합니다.
2. **윈도우 내 연산 수행**: 현재 윈도우의 데이터를 이용하여 필요한 계산을 합니다.
3. **윈도우 이동**: 다음 구간으로 윈도우를 이동합니다.
    - 고정 크기 윈도우인 경우 시작점과 끝점을 한 칸씩 이동합니다.
    - 가변 크기 윈도우인 경우 조건에 따라 윈도우의 크기를 조정합니다.
4. **종료 조건**: 전체 데이터를 모두 처리할 때까지 반복합니다.

## 예제 1: 고정 크기 윈도우 - 최대 부분 합 구하기

### 문제 설명

정수로 이루어진 배열이 주어졌을 때, 연속된 `k`개의 원소의 합이 최대가 되는 값을 구하시오.

### 코드 구현 (Java)
```Java
public class MaxSubarraySum {
    public static int maxSubarraySum(int[] nums, int k) {
        if (nums.length < k) {
            System.out.println("배열의 크기가 k보다 작습니다.");
            return -1;
        }

        // 초기 윈도우 설정
        int maxSum = 0;
        for (int i = 0; i < k; i++) {
            maxSum += nums[i];
        }

        int windowSum = maxSum;

        // 슬라이딩 윈도우 시작
        for (int i = k; i < nums.length; i++) {
            windowSum += nums[i] - nums[i - k];
            maxSum = Math.max(maxSum, windowSum);
        }

        return maxSum;
    }

    public static void main(String[] args) {
        int[] nums = {2, 3, 4, 2, 5, 1, 2, 6, 7};
        int k = 3;

        int result = maxSubarraySum(nums, k);

        System.out.println("연속된 " + k + "개의 원소의 최대 합: " + result);
    }
}

```

### 코드 설명

- **초기 합 계산**: 배열의 처음 `k`개의 원소 합을 계산하여 `maxSum`과 `windowSum`에 저장합니다.
- **슬라이딩 윈도우**:
    - `i`를 `k`부터 시작하여 배열의 끝까지 순회합니다.
    - `windowSum`에 `nums[i]`를 더하고 `nums[i - k]`를 빼서 새로운 윈도우의 합을 구합니다.
    - `maxSum`과 `windowSum`을 비교하여 더 큰 값을 `maxSum`에 저장합니다.

## 예제 2: 가변 크기 윈도우 - 최소 길이의 부분 배열 합 구하기

### 문제 설명

양의 정수로 이루어진 배열에서 합이 `s` 이상이 되는 부분 배열 중 최소 길이를 구하시오.

### 코드 구현 (Java)
```Java
public class MinSubarrayLen {
    public static int minSubArrayLen(int s, int[] nums) {
        int n = nums.length;
        int minLength = n + 1;
        int sum = 0;
        int left = 0;

        // 슬라이딩 윈도우 시작
        for (int right = 0; right < n; right++) {
            sum += nums[right];

            // 합이 s 이상인 경우 윈도우 크기를 줄여 최소 길이 탐색
            while (sum >= s) {
                minLength = Math.min(minLength, right - left + 1);
                sum -= nums[left++];
            }
        }

        return (minLength == n + 1) ? 0 : minLength;
    }

    public static void main(String[] args) {
        int[] nums = {2, 1, 5, 2, 8};
        int s = 7;

        int result = minSubArrayLen(s, nums);

        if (result != 0) {
            System.out.println("합이 " + s + " 이상이 되는 최소 길이: " + result);
        } else {
            System.out.println("조건을 만족하는 부분 배열이 없습니다.");
        }
    }
}

```
### 코드 설명

- **포인터 초기화**: `left`는 윈도우의 시작 인덱스, `right`는 윈도우의 끝 인덱스로 사용합니다.
- **윈도우 확장**: `sum`에 `nums[right]`를 더해 윈도우를 오른쪽으로 확장합니다.
- **윈도우 축소**:
    - 합이 `s` 이상이면 현재 윈도우의 길이를 계산하여 `minLength`를 업데이트합니다.
    - `sum`에서 `nums[left]`를 빼고 `left`를 증가시켜 윈도우를 왼쪽으로 축소합니다.
    - 이 과정을 합이 `s` 미만이 될 때까지 반복합니다.
- **결과 반환**: `minLength`가 초기값이면 조건을 만족하는 부분 배열이 없는 것이므로 `0`을 반환합니다.

## 예제 3: 최대 길이의 고유한 문자로 구성된 부분 문자열 찾기

### 문제 설명

주어진 문자열에서 모든 문자가 고유한 가장 긴 부분 문자열의 길이를 구하시오.

### 코드 구현 (Java)
```Java
import java.util.HashSet;
import java.util.Set;

public class LongestUniqueSubstring {
    public static int lengthOfLongestSubstring(String s) {
        int maxLength = 0;
        Set<Character> charSet = new HashSet<>();

        int left = 0;
        int right = 0;
        int n = s.length();

        while (right < n) {
            if (!charSet.contains(s.charAt(right))) {
                charSet.add(s.charAt(right));
                right++;
                maxLength = Math.max(maxLength, right - left);
            } else {
                charSet.remove(s.charAt(left));
                left++;
            }
        }

        return maxLength;
    }

    public static void main(String[] args) {
        String s = "abcabcbb";

        int result = lengthOfLongestSubstring(s);

        System.out.println("가장 긴 고유 문자 부분 문자열의 길이: " + result);
    }
}

```
### 코드 설명

- **자료 구조 사용**: 현재 윈도우 내의 문자를 저장하기 위해 `HashSet`을 사용합니다.
- **윈도우 확장 및 축소**:
    - `s.charAt(right)`가 `charSet`에 없으면 추가하고 `right`를 증가시킵니다.
    - 이미 존재하면 `s.charAt(left)`를 제거하고 `left`를 증가시켜 윈도우를 축소합니다.
- **최대 길이 업데이트**: 윈도우의 크기가 갱신될 때마다 `maxLength`를 업데이트합니다.

## 슬라이딩 윈도우의 장단점

### 장점

- **효율성**: 전체 데이터를 반복적으로 처리하지 않고 윈도우 내에서 필요한 연산만 수행하여 시간 복잡도를 줄일 수 있습니다.
- **간단한 구현**: 비교적 간단한 코드로 알고리즘을 구현할 수 있습니다.
- **메모리 사용량 적음**: 추가적인 메모리 사용이 최소화됩니다.

### 단점

- **제약 조건 필요**: 슬라이딩 윈도우를 적용하기 위해서는 문제에 연속된 구간에 대한 처리가 가능해야 합니다.
- **복잡한 조건 처리 어려움**: 윈도우 내에서 복잡한 조건을 처리해야 하는 경우 알고리즘이 복잡해질 수 있습니다.

## 슬라이딩 윈도우를 효과적으로 사용하는 방법

- **문제의 특성 파악**: 윈도우를 적용할 수 있는 연속된 데이터 처리가 필요한지 판단합니다.
- **윈도우 크기 결정**: 고정 크기인지 가변 크기인지에 따라 알고리즘을 설계합니다.
- **자료 구조 활용**: 필요에 따라 `HashSet`, `HashMap` 등의 자료 구조를 활용하여 윈도우 내 데이터를 효율적으로 관리합니다.
- **경계 조건 주의**: 윈도우의 시작과 끝 인덱스가 배열의 범위를 벗어나지 않도록 주의합니다.

## 결론

슬라이딩 윈도우 알고리즘은 배열이나 문자열에서 연속된 구간을 효율적으로 처리하는 데 매우 유용한 기법입니다. 윈도우를 이동시키면서 필요한 연산을 수행함으로써 시간 복잡도를 크게 줄일 수 있습니다. 문제의 특성에 따라 윈도우의 크기와 이동 방법을 적절히 설계하여 다양한 문제를 효과적으로 해결할 수 있습니다.