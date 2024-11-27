# 강결합 요소(SCC) 찾기

**강결합 요소(Strongly Connected Components, SCC)**는 방향 그래프에서 모든 노드들이 서로 도달 가능한 부분 그래프를 의미합니다. 즉, 강결합 요소 내의 임의의 두 노드 u와 v에 대해, u에서 v로 가는 경로와 v에서 u로 가는 경로가 모두 존재합니다.

## 개념

- **강결합 요소(SCC)**: 방향 그래프에서 각 노드들이 서로 도달 가능한 최대 부분 그래프.
- **방향 그래프(Directed Graph)**: 간선들이 방향성을 가지는 그래프.
- **도달 가능성(Reachability)**: 한 노드에서 다른 노드로 이동할 수 있는 경로의 존재 여부.

## 특징

- **분해 가능성**: 방향 그래프는 강결합 요소들의 집합으로 분해될 수 있습니다.
- **DAG 구성**: 강결합 요소들을 하나의 노드로 간주하면, 그래프는 방향 비순환 그래프(DAG)가 됩니다.
- **응용 분야**: 순환 의존성 검출, 웹 크롤링, 전기 회로 분석 등.

## 강결합 요소를 찾는 알고리즘

강결합 요소를 찾기 위한 대표적인 알고리즘은 다음과 같습니다:

1. **코사라주 알고리즘(Kosaraju's Algorithm)**
2. **타잔 알고리즘(Tarjan's Algorithm)**
3. **Gabow 알고리즘**

### 1. 코사라주 알고리즘(Kosaraju's Algorithm)

**개념**: 두 번의 DFS를 이용하여 강결합 요소를 찾는 알고리즘입니다.

#### 알고리즘 단계

1. **첫 번째 DFS**:
    - 그래프의 모든 노드를 방문하면서 DFS를 수행하고, 각 노드의 방문이 종료되는 순서대로 스택에 저장합니다.
2. **그래프 전치(Transpose Graph)**:
    - 원본 그래프의 모든 간선의 방향을 뒤집은 그래프를 생성합니다.
3. **두 번째 DFS**:
    - 스택에서 노드를 하나씩 꺼내면서, 전치 그래프에서 DFS를 수행합니다.
    - 이때 방문하는 노드들이 하나의 강결합 요소를 이룹니다.

#### 시간 복잡도

- O(V + E)
    - V: 노드의 수
    - E: 간선의 수

### 2. 타잔 알고리즘(Tarjan's Algorithm)

**개념**: DFS를 한 번만 수행하면서 강결합 요소를 찾는 알고리즘입니다.

#### 알고리즘 단계

1. **DFS 수행**:
    - 각 노드에 대해 DFS를 수행하면서, 방문 순서 번호와 저위값(lowlink)을 기록합니다.
    - 스택을 사용하여 현재 탐색 중인 노드들을 추적합니다.
2. **강결합 요소 식별**:
    - DFS 중에 노드의 저위값이 자신의 방문 순서 번호와 같으면, 스택에서 노드를 꺼내면서 강결합 요소를 형성합니다.

#### 시간 복잡도
- O(V + E)

## 자바로 구현하기

### 코사라주 알고리즘 구현
```Java
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class KosarajuSCC {
    private int V; // 노드의 개수
    private List<List<Integer>> adj; // 인접 리스트

    // 생성자
    public KosarajuSCC(int V) {
        this.V = V;
        adj = new ArrayList<>();
        for (int i = 0; i < V; i++) {
            adj.add(new ArrayList<>());
        }
    }

    // 간선 추가 메서드
    public void addEdge(int u, int v) {
        adj.get(u).add(v);
    }

    // DFS 수행
    private void DFSUtil(int v, boolean[] visited, Stack<Integer> stack) {
        visited[v] = true;
        for (int n : adj.get(v)) {
            if (!visited[n]) {
                DFSUtil(n, visited, stack);
            }
        }
        stack.push(v);
    }

    // 그래프 전치
    private KosarajuSCC getTranspose() {
        KosarajuSCC g = new KosarajuSCC(V);
        for (int v = 0; v < V; v++) {
            for (int n : adj.get(v)) {
                g.addEdge(n, v);
            }
        }
        return g;
    }

    // 강결합 요소 출력
    public void printSCCs() {
        Stack<Integer> stack = new Stack<>();

        // 1. 첫 번째 DFS
        boolean[] visited = new boolean[V];
        for (int i = 0; i < V; i++) {
            if (!visited[i]) {
                DFSUtil(i, visited, stack);
            }
        }

        // 2. 그래프 전치
        KosarajuSCC gr = getTranspose();

        // 3. 두 번째 DFS 준비
        visited = new boolean[V];

        // 4. 스택에서 노드를 하나씩 꺼내면서 DFS 수행
        System.out.println("강결합 요소(SCC):");
        while (!stack.isEmpty()) {
            int v = stack.pop();
            if (!visited[v]) {
                Stack<Integer> componentStack = new Stack<>();
                gr.DFSUtil(v, visited, componentStack);
                // 강결합 요소 출력
                while (!componentStack.isEmpty()) {
                    System.out.print(componentStack.pop() + " ");
                }
                System.out.println();
            }
        }
    }

    // 테스트를 위한 main 메서드
    public static void main(String[] args) {
        KosarajuSCC g = new KosarajuSCC(5);
        g.addEdge(1, 0);
        g.addEdge(0, 2);
        g.addEdge(2, 1);
        g.addEdge(0, 3);
        g.addEdge(3, 4);

        g.printSCCs();
    }
}

```

### 코드 설명

- **`KosarajuSCC` 클래스**: 코사라주 알고리즘을 구현한 클래스입니다.
- **`addEdge` 메서드**: 방향 그래프의 간선을 추가합니다.
- **`DFSUtil` 메서드**: DFS를 수행하고, 노드 방문 순서를 스택에 저장합니다.
- **`getTranspose` 메서드**: 그래프의 전치(간선의 방향을 반대로) 그래프를 생성합니다.
- **`printSCCs` 메서드**: 코사라주 알고리즘에 따라 강결합 요소를 찾아 출력합니다.
    - 첫 번째 DFS로 노드 방문 순서를 스택에 저장합니다.
    - 그래프를 전치한 후, 스택에서 노드를 꺼내며 두 번째 DFS를 수행합니다.
    - 각 DFS에서 방문한 노드들이 하나의 강결합 요소를 이룹니다.

### 타잔 알고리즘 구현
```Java
import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class TarjanSCC {
    private int V; // 노드의 개수
    private List<List<Integer>> adj; // 인접 리스트
    private int time;
    private int[] disc; // 발견 시간
    private int[] low; // 저위값
    private boolean[] inStack; // 스택 포함 여부
    private Stack<Integer> stack;

    // 생성자
    public TarjanSCC(int V) {
        this.V = V;
        adj = new ArrayList<>();
        for (int i = 0; i < V; i++) {
            adj.add(new ArrayList<>());
        }
        time = 0;
        disc = new int[V];
        low = new int[V];
        inStack = new boolean[V];
        stack = new Stack<>();
    }

    // 간선 추가 메서드
    public void addEdge(int u, int v) {
        adj.get(u).add(v);
    }

    // SCC 찾기
    public void SCC() {
        for (int i = 0; i < V; i++) {
            if (disc[i] == 0) {
                SCCUtil(i);
            }
        }
    }

    // SCC를 찾기 위한 재귀 함수
    private void SCCUtil(int u) {
        disc[u] = low[u] = ++time;
        stack.push(u);
        inStack[u] = true;

        for (int v : adj.get(u)) {
            if (disc[v] == 0) {
                SCCUtil(v);
                low[u] = Math.min(low[u], low[v]);
            } else if (inStack[v]) {
                low[u] = Math.min(low[u], disc[v]);
            }
        }

        // 루트 노드인 경우 SCC 출력
        if (low[u] == disc[u]) {
            System.out.print("강결합 요소(SCC): ");
            int w;
            do {
                w = stack.pop();
                inStack[w] = false;
                System.out.print(w + " ");
            } while (w != u);
            System.out.println();
        }
    }

    // 테스트를 위한 main 메서드
    public static void main(String[] args) {
        TarjanSCC g = new TarjanSCC(5);
        g.addEdge(1, 0);
        g.addEdge(0, 2);
        g.addEdge(2, 1);
        g.addEdge(0, 3);
        g.addEdge(3, 4);

        g.SCC();
    }
}

```
### 코드 설명

- **`TarjanSCC` 클래스**: 타잔 알고리즘을 구현한 클래스입니다.
- **멤버 변수**:
    - `disc`: 각 노드의 발견 시간 저장.
    - `low`: 각 노드의 저위값 저장.
    - `inStack`: 노드가 현재 스택에 있는지 여부.
    - `stack`: 현재 탐색 중인 노드들의 스택.
- **`SCCUtil` 메서드**:
    - DFS를 수행하면서 발견 시간과 저위값을 갱신합니다.
    - 저위값이 자신의 발견 시간과 같은 노드는 강결합 요소의 루트 노드입니다.
    - 스택에서 노드를 꺼내며 강결합 요소를 출력합니다.
## 시간 복잡도 분석

- **코사라주 알고리즘**: 두 번의 DFS를 수행하므로 시간 복잡도는 O(V + E)입니다.
- **타잔 알고리즘**: 한 번의 DFS로 강결합 요소를 찾으므로 시간 복잡도는 O(V + E)입니다.

## 강결합 요소 찾기 알고리즘 사용 시 고려사항

- **그래프의 표현**:
    - 인접 리스트를 사용하여 그래프를 표현하면 메모리 효율성과 시간 효율성이 좋습니다.
- **재귀 깊이**:
    - 노드의 수가 매우 큰 경우 재귀 호출로 인해 스택 오버플로우가 발생할 수 있으므로, 반복문으로 변환하거나 스택 크기를 조정해야 합니다.
- **음수 가중치**:
    - 강결합 요소 찾기 알고리즘은 그래프의 가중치에 영향을 받지 않으므로, 가중치가 음수여도 문제없이 동작합니다.

## 응용 분야

- **순환 의존성 검출**: 소프트웨어 모듈 간의 순환 참조를 찾아내어 설계를 개선할 수 있습니다.
- **웹 크롤링**: 웹 페이지 간의 연결 구조에서 강결합 요소를 찾아 관련된 페이지들을 그룹화할 수 있습니다.
- **전기 회로 분석**: 회로 내의 순환 경로를 찾아내어 회로의 특성을 분석할 수 있습니다.
- **데드락 검출**: 자원 할당 그래프에서 강결합 요소를 찾아 데드락 가능성을 판단할 수 있습니다.

## 결론

강결합 요소(SCC)는 방향 그래프에서 중요한 개념으로, 다양한 분야에서 활용됩니다. 코사라주 알고리즘과 타잔 알고리즘은 각각 두 번의 DFS와 한 번의 DFS로 강결합 요소를 찾을 수 있으며, 시간 복잡도는 O(V + E)로 효율적입니다. 그래프 이론과 알고리즘을 이해하고 활용하여 복잡한 문제를 해결하는 데 도움이 됩니다.