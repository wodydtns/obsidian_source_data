**Back Pressure란?**
시스템에서 데이터 생산 속도가 소비 속도보다 빠를 때 발생하는 압력을 관리하는 메커니즘입니다. 데이터 처리 시스템에서 과부하를 방지하고 안정성을 유지하기 위한 중요한 개념입니다.

**Back Pressure가 필요한 이유**
1. 메모리 한계 관리
2. 시스템 안정성 확보
3. 데이터 손실 방지
4. 전체 시스템 성능 최적화

**Back Pressure 구현 전략**

1. **Buffer 전략**
```java
// BlockingQueue를 사용한 버퍼링 예시
BlockingQueue<Data> queue = new ArrayBlockingQueue<>(1000);

// 생산자
void produce() {
    while (true) {
        Data data = generateData();
        if (!queue.offer(data)) {
            // 큐가 가득 찼을 때의 처리
            handleBackPressure();
        }
    }
}

// 소비자
void consume() {
    while (true) {
        Data data = queue.take();
        processData(data);
    }
}
```

2. **Throttling 전략**
```java
// 처리량을 제한하는 예시
RateLimiter rateLimiter = RateLimiter.create(100); // 초당 100개로 제한

void process() {
    if (rateLimiter.tryAcquire()) {
        // 데이터 처리
        processData();
    } else {
        // 처리 거부 또는 대기
        handleThrottling();
    }
}
```

3. **Drop 전략**
```java
class DropStrategy<T> {
    private final Queue<T> queue;
    private final int maxSize;

    public boolean offer(T item) {
        if (queue.size() >= maxSize) {
            queue.poll(); // 가장 오래된 항목 제거
        }
        return queue.offer(item);
    }
}
```

**Reactive Streams에서의 Back Pressure**

Reactive Streams API는 back pressure를 기본적으로 지원합니다:

```java
// Reactive Streams 예시
Flowable.range(1, 1000000)
    .onBackpressureBuffer(1000) // 버퍼 크기 지정
    .observeOn(Schedulers.computation())
    .subscribe(new Subscriber<Integer>() {
        @Override
        public void onSubscribe(Subscription s) {
            s.request(100); // 초기 요청량 설정
        }

        @Override
        public void onNext(Integer value) {
            // 데이터 처리
            processData(value);
            // 추가 데이터 요청
            subscription.request(1);
        }
        // ... 다른 메서드 구현
    });
```

**Back Pressure 패턴**

1. **Push-Pull 패턴**
```java
class PushPullPattern {
    private final BlockingQueue<Data> queue;
    private final int batchSize;

    void consume() {
        List<Data> batch = new ArrayList<>(batchSize);
        while (batch.size() < batchSize) {
            Data data = queue.poll();
            if (data != null) {
                batch.add(data);
            }
        }
        processBatch(batch);
    }
}
```

2. **Credit-Based 플로우 컨트롤**
```java
class CreditBasedFlow {
    private final AtomicInteger credits;
    
    void requestMore(int n) {
        credits.addAndGet(n);
        tryProcessing();
    }

    void tryProcessing() {
        while (credits.get() > 0) {
            if (processOneItem()) {
                credits.decrementAndGet();
            }
        }
    }
}
```

**모니터링과 튜닝**
- 큐 크기 모니터링
- 처리 지연시간 측정
- 메모리 사용량 추적
- 스레드 풀 상태 확인

```java
// 모니터링 예시
class MonitoredQueue<T> {
    private final Queue<T> queue;
    private final Meter offerMeter;
    private final Meter pollMeter;

    public boolean offer(T item) {
        boolean result = queue.offer(item);
        offerMeter.mark();
        return result;
    }

    public T poll() {
        T item = queue.poll();
        if (item != null) {
            pollMeter.mark();
        }
        return item;
    }
}
```
