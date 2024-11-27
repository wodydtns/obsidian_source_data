sentinel 노드(또는 더미 노드)는 데이터 구조의 시작 또는 끝을 표시하기 위해 사용되는 특별한 노드입니다. 실제 데이터를 포함하지 않고, 구현을 단순화하고 경계 조건(edge case) 처리를 쉽게 만드는 것이 주요 목적입니다.

sentinel 노드의 장점을 코드로 설명해드리겠습니다:

```java
// 1. Sentinel 노드를 사용하지 않는 연결 리스트
class LinkedListWithoutSentinel<T> {
    private Node<T> head;
    
    private static class Node<T> {
        T value;
        Node<T> next;
        
        Node(T value) {
            this.value = value;
            this.next = null;
        }
    }
    
    public void insert(T value) {
        Node<T> newNode = new Node<>(value);
        
        // 빈 리스트 처리를 위한 특별한 경우가 필요
        if (head == null) {
            head = newNode;
            return;
        }
        
        // 일반적인 삽입 로직
        newNode.next = head;
        head = newNode;
    }
    
    public void delete(T value) {
        // 빈 리스트 체크 필요
        if (head == null) return;
        
        // head를 삭제하는 특별한 경우 처리 필요
        if (head.value.equals(value)) {
            head = head.next;
            return;
        }
        
        // 일반적인 삭제 로직
        Node<T> current = head;
        while (current.next != null) {
            if (current.next.value.equals(value)) {
                current.next = current.next.next;
                return;
            }
            current = current.next;
        }
    }
}

// 2. Sentinel 노드를 사용하는 연결 리스트
class LinkedListWithSentinel<T> {
    // sentinel 노드로 초기화
    private final Node<T> sentinel = new Node<>(null);
    
    private static class Node<T> {
        T value;
        Node<T> next;
        
        Node(T value) {
            this.value = value;
            this.next = null;
        }
    }
    
    public void insert(T value) {
        Node<T> newNode = new Node<>(value);
        // 항상 동일한 로직으로 처리 가능
        newNode.next = sentinel.next;
        sentinel.next = newNode;
    }
    
    public void delete(T value) {
        // 항상 sentinel부터 시작하므로 빈 리스트 체크 불필요
        Node<T> current = sentinel;
        
        // 모든 케이스를 동일한 로직으로 처리 가능
        while (current.next != null) {
            if (current.next.value.equals(value)) {
                current.next = current.next.next;
                return;
            }
            current = current.next;
        }
    }
}

// 3. 동시성 큐에서 sentinel 노드의 사용 예
class ConcurrentQueueExample<T> {
    private static class Node<T> {
        T value;
        AtomicReference<Node<T>> next;
        
        Node(T value) {
            this.value = value;
            this.next = new AtomicReference<>(null);
        }
    }
    
    private final AtomicReference<Node<T>> head;
    private final AtomicReference<Node<T>> tail;
    
    public ConcurrentQueueExample() {
        // sentinel 노드 생성
        Node<T> sentinel = new Node<>(null);
        head = new AtomicReference<>(sentinel);
        tail = new AtomicReference<>(sentinel);
    }
    
    public void enqueue(T value) {
        Node<T> newNode = new Node<>(value);
        while (true) {
            Node<T> curTail = tail.get();
            // tail이 항상 존재함이 보장되어 null 체크 불필요
            if (curTail.next.compareAndSet(null, newNode)) {
                tail.compareAndSet(curTail, newNode);
                return;
            }
        }
    }
}

```

sentinel 노드를 사용하는 주요 이점:

1. **코드 단순화**
   - 빈 리스트 검사가 불필요
   - 특별한 경우(edge case) 처리가 줄어듦
   - 삽입/삭제 로직이 통일됨

2. **동시성 처리 용이**
   - head와 tail이 항상 존재함이 보장됨
   - null 체크가 줄어듦
   - 경쟁 조건(race condition) 처리가 단순화됨

3. **에러 방지**
   - null 포인터 예외 위험 감소
   - 경계 조건에서의 버그 가능성 감소

4. **성능 향상**
   - 조건 검사 감소
   - 코드 실행 경로 단순화

반면 단점은:

1. 추가 메모리 사용 (노드 하나 추가)
2. 개념적 복잡성 증가
3. 값 검색 시 sentinel 노드 고려 필요

마이클-스콧 큐에서 sentinel 노드는 특히 중요한데, 이는 큐가 비어있을 때의 처리를 단순화하고 동시성 처리를 더 안정적으로 만들어주기 때문입니다.