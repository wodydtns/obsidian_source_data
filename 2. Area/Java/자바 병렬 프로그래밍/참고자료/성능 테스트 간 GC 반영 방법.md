---
제목: 성능 테스트 간 GC 반영 방법
Chapter: 12장
---
1. GC가 실행되지 않도록 하는 방법
    - 테스트 전에 `System.gc()`를 호출하여 GC를 강제로 실행합니다.
    - 사용 가능한 메모리의 대부분을 미리 할당하여 테스트 중 새로운 메모리 할당을 최소화합니다.
    - 테스트를 실행합니다.
    - 테스트가 끝난 후 할당된 메모리를 해제합니다.
    - 예시
        
        ```Java
        import java.util.ArrayList;
        import java.util.List;
        
        public class GCControlExample {
            private static final int ALLOCATION_SIZE = 1024 * 1024; // 1MB
            private static List<byte[]> memoryConsumers = new ArrayList<>();
        
            public static void main(String[] args) {
                // 1. 테스트 전 GC 강제 실행
                System.gc();
                System.runFinalization();
        
                // 2. 메모리 미리 할당
                allocateMemory();
        
                // 3. 테스트 실행
                long startTime = System.nanoTime();
                runTest();
                long endTime = System.nanoTime();
        
                // 4. 결과 출력
                System.out.println("Test duration: " + (endTime - startTime) / 1_000_000 + " ms");
        
                // 5. 할당된 메모리 해제
                memoryConsumers.clear();
                System.gc();
            }
        
            private static void allocateMemory() {
                // 사용 가능한 메모리의 대부분을 할당
                long maxMemory = Runtime.getRuntime().maxMemory();
                long allocatedMemory = 0;
                while (allocatedMemory < maxMemory * 0.8) { // 최대 메모리의 80%까지 할당
                    memoryConsumers.add(new byte[ALLOCATION_SIZE]);
                    allocatedMemory += ALLOCATION_SIZE;
                }
            }
        
            private static void runTest() {
                // 여기에 실제 테스트 로직을 구현
                for (int i = 0; i < 1000000; i++) {
                    Math.sqrt(i);
                }
            }
        }
        ```
        

2.가비지 컬렉션 작업을 명확히 하고 테스트 결과에 반영하는 방법

- 테스트 시작 전과 후에 GC 시간과 횟수를 측정합니다.
- 테스트 중에 발생한 GC의 총 시간과 횟수를 계산합니다.
- 전체 테스트 시간에서 GC 시간을 제외한 순수 테스트 시간을 계산합니다.
- 예시
    
    ```Java
    import java.util.ArrayList;
    import java.util.List;
    
    public class GCControlExample {
        private static final int ALLOCATION_SIZE = 1024 * 1024; // 1MB
        private static List<byte[]> memoryConsumers = new ArrayList<>();
    
        public static void main(String[] args) {
            // 1. 테스트 전 GC 강제 실행
            System.gc();
            System.runFinalization();
    
            // 2. 메모리 미리 할당
            allocateMemory();
    
            // 3. 테스트 실행
            long startTime = System.nanoTime();
            runTest();
            long endTime = System.nanoTime();
    
            // 4. 결과 출력
            System.out.println("Test duration: " + (endTime - startTime) / 1_000_000 + " ms");
    
            // 5. 할당된 메모리 해제
            memoryConsumers.clear();
            System.gc();
        }
    
        private static void allocateMemory() {
            // 사용 가능한 메모리의 대부분을 할당
            long maxMemory = Runtime.getRuntime().maxMemory();
            long allocatedMemory = 0;
            while (allocatedMemory < maxMemory * 0.8) { // 최대 메모리의 80%까지 할당
                memoryConsumers.add(new byte[ALLOCATION_SIZE]);
                allocatedMemory += ALLOCATION_SIZE;
            }
        }
    
        private static void runTest() {
            // 여기에 실제 테스트 로직을 구현
            for (int i = 0; i < 1000000; i++) {
                Math.sqrt(i);
            }
        }
    }
    ```