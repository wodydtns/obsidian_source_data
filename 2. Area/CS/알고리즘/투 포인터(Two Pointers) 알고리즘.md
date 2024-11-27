- **투 포인터(Two Pointers)** 알고리즘은 배열이나 리스트를 탐색할 때, 두 개의 포인터를 이용하여 효율적으로 원하는 결과를 찾는 알고리즘 기법입니다. 이 방법은 주로 정렬된 배열에서 부분 배열의 합, 특정 조건을 만족하는 구간 등을 찾을 때 사용됩니다.

## 개념

- **두 개의 포인터 사용**: 시작점과 끝점 등 두 개의 인덱스를 사용하여 데이터를 탐색합니다.
- **효율성 향상**: 중첩된 반복문을 사용하지 않고 O(N) 또는 O(N log N)의 시간 복잡도로 문제를 해결할 수 있습니다.

## 적용 분야

- **부분 합 문제**: 배열 내의 연속된 부분 배열의 합이 특정 값을 갖는 경우를 찾는 문제.
- **정렬된 배열에서의 합**: 두 수의 합이 특정 값이 되는 모든 쌍을 찾는 문제.
- **중복된 원소 제거**: 배열에서 중복된 원소를 제거하는 문제.
- **팰린드롬 검사**: 문자열이 회문인지 검사하는 문제.

## 알고리즘 과정

1. **초기화**: 두 개의 포인터를 설정합니다.
    - 예를 들어, `left`는 시작 인덱스, `right`는 끝 인덱스로 설정합니다.
2. **조건 검사**: 현재 포인터들이 가리키는 원소나 인덱스의 값을 검사합니다.
3. **포인터 이동**: 조건에 따라 포인터를 이동시킵니다.
    - 조건을 만족하면 결과를 저장하거나 출력하고, 포인터를 이동합니다.
4. **종료 조건**: 포인터들이 배열의 끝에 도달하거나 특정 조건을 만족하면 알고리즘을 종료합니다.

## 예제 1: 정렬된 배열에서 특정 합을 갖는 원소의 쌍 찾기

### 문제 설명

정렬된 배열이 주어졌을 때, 두 수의 합이 특정 값 `target`이 되는 모든 원소의 쌍을 찾으시오.

### 코드 구현 (Java)
```Java
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class TwoPointersExample {
    public static List<int[]> findPairs(int[] array, int target) {
        List<int[]> result = new ArrayList<>();
        int left = 0;
        int right = array.length - 1;

        Arrays.sort(array); // 배열이 정렬되지 않았다면 정렬합니다.

        while (left < right) {
            int sum = array[left] + array[right];
            if (sum == target) {
                result.add(new int[]{array[left], array[right]});
                left++;
                right--;
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
        return result;
    }

    public static void main(String[] args) {
        int[] array = {1, 3, 2, 5, 46, 6, 7, 4};
        int target = 7;

        List<int[]> pairs = findPairs(array, target);

        System.out.println("합이 " + target + "인 원소의 쌍:");
        for (int[] pair : pairs) {
            System.out.println(pair[0] + ", " + pair[1]);
        }
    }
}

```
### 코드 설명

- **정렬**: 배열이 정렬되지 않았다면 정렬합니다.
- **포인터 초기화**: `left`는 배열의 시작 인덱스, `right`는 배열의 끝 인덱스로 설정합니다.
- **반복**: `left`가 `right`보다 작은 동안 반복합니다.
    - 현재 `left`와 `right`가 가리키는 원소의 합을 계산합니다.
    - 합이 `target`과 같으면 결과 리스트에 추가하고 양쪽 포인터를 이동합니다.
    - 합이 `target`보다 작으면 `left`를 증가시켜 합을 키웁니다.
    - 합이 `target`보다 크면 `right`를 감소시켜 합을 줄입니다.

## 예제 2: 부분 합 구하기

### 문제 설명

양의 정수로 이루어진 배열에서 연속된 부분 배열의 합이 `target` 이상이 되는 최소 길이를 구하시오.

## 코드 구현 (Java)
```Java
public class SubarraySum {
    public static int minSubArrayLen(int target, int[] nums) {
        int n = nums.length;
        int minLength = n + 1;
        int sum = 0;
        int left = 0;

        for (int right = 0; right < n; right++) {
            sum += nums[right];

            while (sum >= target) {
                minLength = Math.min(minLength, right - left + 1);
                sum -= nums[left++];
            }
        }

        return (minLength == n + 1) ? 0 : minLength;
    }

    public static void main(String[] args) {
        int[] nums = {2, 3, 1, 2, 4, 3};
        int target = 7;

        int length = minSubArrayLen(target, nums);

        System.out.println("합이 " + target + " 이상이 되는 최소 부분 배열의 길이: " + length);
    }
}

```
### 코드 설명

- **포인터 초기화**: `left`는 부분 배열의 시작 인덱스, `right`는 끝 인덱스로 사용합니다.
- **합 계산**: `sum`에 `nums[right]`를 더해 현재 부분 배열의 합을 구합니다.
- **조건 검사 및 포인터 이동**:
    - 합이 `target` 이상이면 현재 부분 배열의 길이를 업데이트하고, `sum`에서 `nums[left]`를 빼고 `left`를 증가시킵니다.
    - 이 과정을 합이 `target` 미만이 될 때까지 반복합니다.
- **최소 길이 반환**: 최소 길이를 반환합니다. 만약 존재하지 않으면 `0`을 반환합니다.
## 투 포인터의 장단점

### 장점

- **효율성**: 중첩된 반복문을 사용하지 않고도 문제를 해결할 수 있어 시간 복잡도를 줄일 수 있습니다.
- **간단한 구현**: 비교적 간단한 코드로 구현할 수 있습니다.
- **메모리 사용량 적음**: 추가적인 메모리 사용이 거의 없습니다.

### 단점

- **정렬 필요성**: 일부 문제에서는 배열이 정렬되어 있어야 투 포인터를 적용할 수 있습니다.
- **제한된 적용 범위**: 모든 문제에 적용할 수 있는 것은 아니며, 특정 유형의 문제에만 효과적입니다.

## 투 포인터를 효과적으로 사용하는 방법

- **정렬 여부 확인**: 필요한 경우 배열을 정렬합니다.
- **포인터의 의미 파악**: `left`와 `right` 포인터가 무엇을 가리키는지 명확히 이해합니다.
- **조건에 따른 포인터 이동**: 문제의 조건에 따라 포인터를 적절히 이동시킵니다.
- **예외 처리**: 배열의 경계 조건을 주의하여 예외 상황을 처리합니다.

## 결론

투 포인터 알고리즘은 배열이나 리스트를 효율적으로 탐색할 때 매우 유용한 기법입니다. 두 개의 포인터를 사용하여 시간 복잡도를 크게 줄일 수 있으며, 특정 조건을 만족하는 부분 배열이나 원소 쌍을 찾는 문제에서 자주 활용됩니다. 문제의 특성에 따라 포인터의 이동 방법과 초기화를 적절히 설정하여 효율적인 알고리즘을 설계할 수 있습니다.