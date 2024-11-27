```python
def dfs(graph, v, visited):
    # 현재 노드를 방문 처리
    visited[v] = True
    print(v, end=' ')  # 방문한 노드 출력
    
    #현재 노드와 연결된 다른 노드를 재귀적으로 방문
    for i in graph[v]:
        if not visited[i]:
            dfs(graph, i, visited)

# 각 노드가 연결된 정보를 리스트 자료형으로 표현
graph = [
    [],
    [2,3,8],
    [1,7],
    [1,4,5],
    [3,5],
    [3,4],
    [7],
    [2,6,8],
    [1,7]
]

# 각 노드가 방문된 정보를 리스트 자료형으로 표현
visited = [False] * 9

print("DFS 방문 순서: ", end='')
dfs(graph, 1, visited)
```

```Java
import java.util.*;

public class Main {

    public static void dfs(List<List<Integer>> graph, int v, boolean[] visited) {
        // 현재 노드를 방문 처리
        visited[v] = true;
        System.out.print(v + " ");  // 방문한 노드 출력

        // 현재 노드와 연결된 다른 노드를 재귀적으로 방문
        for (int i : graph.get(v)) {
            if (!visited[i]) {
                dfs(graph, i, visited);
            }
        }
    }

    public static void main(String[] args) {
        // 각 노드가 연결된 정보를 리스트 자료형으로 표현
        List<List<Integer>> graph = new ArrayList<>();

        // 그래프 초기화
        for (int i = 0; i < 9; i++) {
            graph.add(new ArrayList<Integer>());
        }

        // 각 노드에 연결된 노드 정보 추가
        graph.get(1).addAll(Arrays.asList(2, 3, 8));
        graph.get(2).addAll(Arrays.asList(1, 7));
        graph.get(3).addAll(Arrays.asList(1, 4, 5));
        graph.get(4).addAll(Arrays.asList(3, 5));
        graph.get(5).addAll(Arrays.asList(3, 4));
        graph.get(6).addAll(Arrays.asList(7));
        graph.get(7).addAll(Arrays.asList(2, 6, 8));
        graph.get(8).addAll(Arrays.asList(1, 7));

        // 각 노드가 방문된 정보를 배열로 표현
        boolean[] visited = new boolean[9];

        System.out.print("DFS 방문 순서: ");
        dfs(graph, 1, visited);
    }
}

```

