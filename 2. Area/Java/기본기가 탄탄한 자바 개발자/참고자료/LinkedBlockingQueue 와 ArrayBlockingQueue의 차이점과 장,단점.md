**LinkedBlockingQueue**

장점:
1. 크기가 동적으로 조절됨 (기본적으로 Integer.MAX_VALUE)
2. 삽입/삭제 작업이 O(1) 시간 복잡도
3. 큐의 앞/뒤에서의 작업이 서로 독립적이라 lock 경합이 적음 (두 개의 별도 lock 사용)

단점:
1. 각 노드마다 추가적인 메모리가 필요 (다음 노드 참조를 위한)
2. GC 부하가 더 큼
3. 크기 제한이 없을 경우 메모리 문제 발생 가능

**ArrayBlockingQueue**

장점:
1. 고정 크기로 메모리 사용량 예측 가능
2. 연속된 메모리 공간 사용으로 캐시 효율성 좋음
3. GC 부하가 적음

단점:
1. 크기가 고정되어 있어 유연성이 떨어짐
2. 큐의 모든 작업이 하나의 lock을 사용 (경합 발생 가능)
3. 큐가 가득 찼을 때 크기를 늘릴 수 없음

**사용 케이스**

- LinkedBlockingQueue:
  - 큐의 크기를 예측하기 어려운 경우
  - 생산자/소비자의 속도 차이가 큰 경우
  - 높은 처리량이 필요한 경우

- ArrayBlockingQueue:
  - 메모리 사용량을 엄격하게 제한해야 하는 경우
  - 큐의 최대 크기를 알고 있는 경우
  - 짧은 지연 시간이 중요한 경우


- 사용 예시
```java
// LinkedBlockingQueue 예시
LinkedBlockingQueue<String> linkedQueue = new LinkedBlockingQueue<>(1000); // 크기 제한 설정
LinkedBlockingQueue<String> unboundedQueue = new LinkedBlockingQueue<>(); // 무제한

// ArrayBlockingQueue 예시
ArrayBlockingQueue<String> arrayQueue = new ArrayBlockingQueue<>(1000); // 반드시 크기 지정 필요

// 기본적인 작업
queue.put("item");     // 큐가 가득 찼을 때 블록킹
queue.take();          // 큐가 비었을 때 블록킹
queue.offer("item");   // 큐가 가득 찼을 때 false 반환
queue.poll();          // 큐가 비었을 때 null 반환
```

