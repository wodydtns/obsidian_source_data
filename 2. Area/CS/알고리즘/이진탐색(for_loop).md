```python
﻿# 이진 탐색 소스코드 구현
def binary_search(array, target, start, end):
    while start <= end:
        mid = (start + end) // 2
        # 찾은 경우 중간점 인덱스 반환
        if array[mid] == target:
            return mid
        #중간점의 값보다 찾고자 하는 값이 작은 경우 왼쪽 확인
        elif array[mid] > target:
            end = mid - 1
        #중간점의 값보다 찾고자 하는 값이 큰 경우 오른쪽 확인
        else:
            start = mid + 1
    return None

# n과 target을 입력 받기
n , target = list(map(int, input().split()))

# 전체 원소 입력받기
array = list(map(int, input().split()))

#이진 탐색 수행 결과 출력
result = binary_search(array, target, 0, n - 1 )
if result == None:
    print('원소가 존재하지 않습니다')
else:
    print(result + 1)
```

```Java
import java.util.Scanner;

public class Main {

    // 이진 탐색 소스코드 구현
    public static Integer binarySearch(int[] array, int target, int start, int end) {
        while (start <= end) {
            int mid = (start + end) / 2;
            // 찾은 경우 중간점 인덱스 반환
            if (array[mid] == target) {
                return mid;
            }
            // 중간점의 값보다 찾고자 하는 값이 작은 경우 왼쪽 확인
            else if (array[mid] > target) {
                end = mid - 1;
            }
            // 중간점의 값보다 찾고자 하는 값이 큰 경우 오른쪽 확인
            else {
                start = mid + 1;
            }
        }
        return null;
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        // n과 target을 입력 받기
        int n = sc.nextInt();
        int target = sc.nextInt();

        // 전체 원소 입력받기
        int[] array = new int[n];
        for (int i = 0; i < n; i++) {
            array[i] = sc.nextInt();
        }

        // 이진 탐색 수행 결과 출력
        Integer result = binarySearch(array, target, 0, n - 1);
        if (result == null) {
            System.out.println("원소가 존재하지 않습니다");
        } else {
            System.out.println(result + 1);
        }

        sc.close();
    }
}

```
