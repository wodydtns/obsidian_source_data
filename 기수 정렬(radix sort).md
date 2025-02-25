```Java
public class RadixSort {
    // 배열에서 최대값 찾기
    private static int getMax(int[] arr) {
        int max = arr[0];
        for (int i = 1; i < arr.length; i++) {
            if (arr[i] > max) {
                max = arr[i];
            }
        }
        return max;
    }
    
    // 특정 자릿수를 기준으로 계수 정렬을 수행
    private static void countSort(int[] arr, int exp) {
        int n = arr.length;
        int[] output = new int[n];
        int[] count = new int[10]; // 0-9까지의 숫자를 저장할 배열
        
        // 현재 자릿수의 값의 발생 횟수를 센다
        for (int i = 0; i < n; i++) {
            count[(arr[i] / exp) % 10]++;
        }
        
        // count 배열을 누적합으로 변환
        for (int i = 1; i < 10; i++) {
            count[i] += count[i - 1];
        }
        
        // 뒤에서부터 순회하여 output 배열에 올바른 위치에 숫자를 배치
        for (int i = n - 1; i >= 0; i--) {
            int digit = (arr[i] / exp) % 10;
            output[count[digit] - 1] = arr[i];
            count[digit]--;
        }
        
        // 정렬된 배열을 원본 배열로 복사
        System.arraycopy(output, 0, arr, 0, n);
    }
    
    // 기수 정렬 메인 메소드
    public static void radixSort(int[] arr) {
        if (arr == null || arr.length <= 1) {
            return;
        }
        
        // 배열에서 최대값 찾기
        int max = getMax(arr);
        
        // 1의 자리부터 시작해서 최대값의 자릿수까지 정렬 수행
        for (int exp = 1; max / exp > 0; exp *= 10) {
            countSort(arr, exp);
        }
    }
    
    // 배열 출력 유틸리티 메소드
    private static void printArray(int[] arr) {
        for (int num : arr) {
            System.out.print(num + " ");
        }
        System.out.println();
    }
    
    // 메인 메소드 - 테스트
    public static void main(String[] args) {
        // 테스트 케이스 1: 일반적인 경우
        int[] arr1 = {170, 45, 75, 90, 802, 24, 2, 66};
        System.out.println("원본 배열 1:");
        printArray(arr1);
        radixSort(arr1);
        System.out.println("정렬된 배열 1:");
        printArray(arr1);
        
        // 테스트 케이스 2: 중복된 숫자가 있는 경우
        int[] arr2 = {123, 321, 123, 432, 543, 321, 234};
        System.out.println("\n원본 배열 2:");
        printArray(arr2);
        radixSort(arr2);
        System.out.println("정렬된 배열 2:");
        printArray(arr2);
        
        // 테스트 케이스 3: 작은 숫자들
        int[] arr3 = {1, 4, 1, 2, 7, 5, 2};
        System.out.println("\n원본 배열 3:");
        printArray(arr3);
        radixSort(arr3);
        System.out.println("정렬된 배열 3:");
        printArray(arr3);
    }
}

```