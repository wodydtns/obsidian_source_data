**세그먼트 트리(Segment Tree)**는 배열과 같은 선형 자료구조에서 **구간에 대한 질의(range query)**와 **구간에 대한 업데이트(range update)**를 효율적으로 수행하기 위한 자료구조입니다. 세그먼트 트리는 트리 형태의 구조를 가지며, 각 노드는 배열의 특정 구간 정보를 저장합니다.

## 개념

- **트리 구조**: 완전 이진 트리로 구현되며, 루트 노드는 전체 배열 구간을 나타냅니다.
- **노드 정보**: 각 노드는 해당 구간의 합, 최소값, 최대값 등 원하는 정보를 저장합니다.
- **구간 분할**: 구간을 재귀적으로 반씩 나누어 왼쪽과 오른쪽 자식 노드로 분할합니다.

## 특징

- **구간 합, 최소/최대값 질의에 효율적**: O(log n)의 시간 복잡도로 구간 질의와 업데이트를 수행할 수 있습니다.
- **동적 배열 지원**: 세그먼트 트리는 동적으로 배열의 크기를 변경하거나 업데이트할 수 있습니다.
- **메모리 사용량**: 일반적으로 4n 크기의 배열로 세그먼트 트리를 구현합니다.

## 적용 분야

- **구간 합(query sum)**: 특정 범위의 합을 구해야 하는 경우.
- **구간 최소/최대값(query min/max)**: 특정 범위 내의 최소값이나 최대값을 구해야 하는 경우.
- **구간 업데이트(range update)**: 특정 범위의 값을 일괄적으로 업데이트해야 하는 경우.
- **문자열의 패턴 매칭**: 문자열에서 특정 패턴이나 구간에 대한 정보를 빠르게 처리해야 하는 경우.

## 세그먼트 트리의 동작 원리

### 1. 빌드(Build)

- 배열의 데이터를 기반으로 세그먼트 트리를 구축합니다.
- 리프 노드는 배열의 원소를 나타내고, 내부 노드는 자식 노드의 정보를 합쳐서 저장합니다.

### 2. 질의(Query)

- 원하는 구간에 대한 정보를 얻기 위해 트리를 탐색합니다.
- 재귀적으로 노드를 방문하며 구간이 겹치는 경우 정보를 합산하거나 비교합니다.

### 3. 업데이트(Update)

- 특정 인덱스나 구간의 값을 변경하고 트리에 반영합니다.
- 변경된 노드부터 루트 노드까지 갱신하여 일관성을 유지합니다.

## 자바로 구현하기

### 세그먼트 트리 클래스 구현

#### 1. 구간 합 세그먼트 트리
```Java
public class SegmentTree {
    private int[] tree;
    private int[] arr;
    private int n;

    // 생성자: 원본 배열로부터 세그먼트 트리 생성
    public SegmentTree(int[] arr) {
        this.arr = arr;
        this.n = arr.length;
        // 트리의 크기를 4n으로 설정
        this.tree = new int[4 * n];
        build(1, 0, n - 1);
    }

    // 세그먼트 트리 구축
    private void build(int node, int start, int end) {
        if (start == end) {
            // 리프 노드
            tree[node] = arr[start];
        } else {
            int mid = (start + end) / 2;
            // 왼쪽 자식 노드
            build(2 * node, start, mid);
            // 오른쪽 자식 노드
            build(2 * node + 1, mid + 1, end);
            // 자식 노드의 합을 저장
            tree[node] = tree[2 * node] + tree[2 * node + 1];
        }
    }

    // 구간 합 질의
    public int query(int l, int r) {
        return query(1, 0, n -1, l, r);
    }

    private int query(int node, int start, int end, int l, int r) {
        // 범위 밖인 경우
        if (r < start || end < l) {
            return 0; // 합에 영향을 주지 않는 값 반환
        }
        // 범위 안인 경우
        if (l <= start && end <= r) {
            return tree[node];
        }
        // 일부 겹치는 경우
        int mid = (start + end) /2;
        int p1 = query(2 * node, start, mid, l, r);
        int p2 = query(2 * node +1, mid +1, end, l, r);
        return p1 + p2;
    }

    // 값 업데이트
    public void update(int idx, int val) {
        update(1, 0, n -1, idx, val);
    }

    private void update(int node, int start, int end, int idx, int val) {
        if (start == end) {
            // 리프 노드 업데이트
            arr[idx] = val;
            tree[node] = val;
        } else {
            int mid = (start + end) /2;
            if (start <= idx && idx <= mid) {
                // 왼쪽 자식 노드 갱신
                update(2 * node, start, mid, idx, val);
            } else {
                // 오른쪽 자식 노드 갱신
                update(2 * node +1, mid +1, end, idx, val);
            }
            // 부모 노드 갱신
            tree[node] = tree[2 * node] + tree[2 * node +1];
        }
    }
}

public class Main {
    public static void main(String[] args) {
        int[] arr = {1,3,5,7,9,11};
        SegmentTree segTree = new SegmentTree(arr);

        // 구간 합 질의: 인덱스 1부터 3까지 (3 + 5 + 7)
        System.out.println("구간 합 (1,3): " + segTree.query(1, 3));

        // 인덱스 1의 값을 10으로 업데이트
        segTree.update(1, 10);

        // 업데이트 후 구간 합 질의: 인덱스 1부터 3까지 (10 + 5 + 7)
        System.out.println("구간 합 (1,3) 업데이트 후: " + segTree.query(1, 3));
    }
}

```

### 코드 설명

- **`SegmentTree` 클래스**: 세그먼트 트리를 구현한 클래스입니다.
- **`build` 메서드**: 세그먼트 트리를 구축합니다.
- **`query` 메서드**: 구간 합 질의를 수행합니다.
- **`update` 메서드**: 특정 인덱스의 값을 업데이트하고 트리에 반영합니다.
- **`Main` 클래스**: 세그먼트 트리를 생성하고 질의와 업데이트를 수행합니다.

## 세그먼트 트리의 시간 복잡도

- **구축 시간**: O(n)
- **구간 질의 시간**: O(log n)
- **업데이트 시간**: O(log n)

## 세그먼트 트리의 변형

### 1. 구간 최소/최대값 세그먼트 트리

- 각 노드에 구간의 최소값 또는 최대값을 저장합니다.
- 질의와 업데이트 함수에서 합 대신 최소값 또는 최대값을 계산합니다.

#### 코드 예시
```Java
// 구간 최소값을 저장하는 세그먼트 트리로 변경
private void build(int node, int start, int end) {
    if (start == end) {
        tree[node] = arr[start];
    } else {
        int mid = (start + end) / 2;
        build(2 * node, start, mid);
        build(2 * node + 1, mid + 1, end);
        tree[node] = Math.min(tree[2 * node], tree[2 * node + 1]);
    }
}

// 구간 최소값 질의 함수
private int query(int node, int start, int end, int l, int r) {
    if (r < start || end < l) {
        return Integer.MAX_VALUE; // 최소값에 영향이 없는 값 반환
    }
    if (l <= start && end <= r) {
        return tree[node];
    }
    int mid = (start + end) / 2;
    int p1 = query(2 * node, start, mid, l, r);
    int p2 = query(2 * node + 1, mid + 1, end, l, r);
    return Math.min(p1, p2);
}

```

### 2. 구간 업데이트를 지원하는 세그먼트 트리 (Lazy Propagation)

- 구간 업데이트를 효율적으로 처리하기 위해 **Lazy Propagation** 기법을 사용합니다.
- 업데이트 시 즉시 트리를 갱신하지 않고, 필요할 때 갱신을 수행합니다.

## 세그먼트 트리 사용 시 고려사항

- **메모리 사용량**: 일반적으로 4n 크기의 배열을 사용하므로 큰 배열의 경우 메모리 사용량이 많을 수 있습니다.
- **구현 복잡도**: 세그먼트 트리는 구현이 비교적 복잡하므로 정확한 이해와 주의가 필요합니다.
- **데이터의 변경 빈도**: 데이터의 변경이 빈번하지 않은 경우 다른 자료구조나 알고리즘이 더 적합할 수 있습니다.

## 세그먼트 트리의 응용 분야

- **온라인 알고리즘**: 실시간으로 데이터가 추가되거나 변경되는 경우.
- **게임 개발**: 맵 데이터나 이벤트 처리 등에서 구간 정보를 효율적으로 관리.
- **문자열 처리**: 문자열의 구간에 대한 패턴 매칭이나 빈도 계산.
- **대형 데이터 처리**: 빅데이터 분석에서 구간 질의가 필요한 경우.

## 결론

세그먼트 트리는 구간에 대한 질의와 업데이트를 효율적으로 처리할 수 있는 강력한 자료구조입니다. 트리 구조를 활용하여 O(log n)의 시간 복잡도로 연산을 수행할 수 있어 많은 알고리즘 문제에서 활용됩니다. 그러나 구현의 복잡성과 메모리 사용량 등을 고려하여 적절한 상황에서 사용하는 것이 중요합니다.

# 참고 자료

- **백준 온라인 저지**: 세그먼트 트리를 활용한 다양한 문제들이 있습니다.
- **GeeksforGeeks**: 세그먼트 트리에 대한 자세한 설명과 예제가 있습니다.
- **알고리즘 강의**: 세그먼트 트리와 Lazy Propagation에 대한 온라인 강의를 참고하면 이해에 도움이 됩니다.

# 마무리

세그먼트 트리는 알고리즘 분야에서 중요한 자료구조 중 하나이며, 다양한 문제 해결에 응용될 수 있습니다. 기본적인 개념과 구현 방법을 익히고, 다양한 변형과 최적화 기법을 공부하면 더욱 복잡한 문제들도 해결할 수 있을 것입니다
