### 개념

- **접미사 트리**: 문자열의 모든 접미사를 트리 형태로 저장한 자료구조로, 각 경로는 문자열의 접미사를 나타냅니다.
- **노드(Node)**: 문자열의 부분 문자열을 나타내며, 간선에는 레이블이 있습니다.
- **루트(Root)**: 빈 문자열을 나타내는 노드로부터 시작합니다.

### 특징

- **빠른 검색**: 문자열의 길이에 선형적으로 비례하는 O(m)의 시간 복잡도로 검색 가능 (m은 패턴의 길이).
- **공간 소모**: 큰 메모리 공간이 필요하며, 실용적인 구현을 위해 Ukkonen의 알고리즘 등을 사용하여 압축합니다.
- **복잡한 구현**: 구현이 비교적 어렵고, 디버깅이 어려울 수 있습니다.

### 접미사 트리 구축 방법

- **Ukkonen의 알고리즘**: O(n)의 시간 복잡도로 접미사 트리를 구축하는 알고리즘.
- **Weiner의 알고리즘**: 최초의 선형 시간 접미사 트리 구축 알고리즘.

### 자바로 구현하기

#### 간단한 접미사 트리 구현
```Java
import java.util.HashMap;
import java.util.Map;

public class SuffixTree {
    // 노드 클래스
    class Node {
        Map<Character, Node> children = new HashMap<>();
        int start;
        int* end;
        Node suffixLink;

        public Node(int start, int* end) {
            this.start = start;
            this.end = end;
        }
    }

    private String text;
    private Node root;

    public SuffixTree(String text) {
        this.text = text;
        buildSuffixTree();
    }

    private void buildSuffixTree() {
        // 접미사 트리 구축 알고리즘 구현 (Ukkonen's Algorithm 등)
        // 구현이 복잡하여 여기서는 생략합니다.
    }

    // 패턴 검색 메서드
    public boolean search(String pattern) {
        Node currentNode = root;
        int i = 0;

        while (i < pattern.length()) {
            char ch = pattern.charAt(i);
            if (currentNode.children.containsKey(ch)) {
                Node child = currentNode.children.get(ch);
                int edgeLength = child.end - child.start + 1;
                int j = 0;
                while (j < edgeLength && i < pattern.length()) {
                    if (text.charAt(child.start + j) != pattern.charAt(i)) {
                        return false;
                    }
                    i++;
                    j++;
                }
                if (j == edgeLength) {
                    currentNode = child;
                } else {
                    return false;
                }
            } else {
                return false;
            }
        }
        return true;
    }

    // 테스트를 위한 main 메서드
    public static void main(String[] args) {
        String text = "banana";
        SuffixTree tree = new SuffixTree(text);

        String pattern = "ana";
        if (tree.search(pattern)) {
            System.out.println("패턴 '" + pattern + "'이(가) 존재합니다.");
        } else {
            System.out.println("패턴 '" + pattern + "'이(가) 존재하지 않습니다.");
        }
    }
}

```

#### 주의 사항

- 접미사 트리의 실제 구현은 매우 복잡하며, 위의 코드는 구조를 간략히 보여주기 위한 것입니다.
- Ukkonen의 알고리즘 등 실제 접미사 트리 구축 알고리즘은 구현이 복잡하여 여기서는 생략하였습니다.
- 접미사 트리의 노드와 간선 관리, 활성점(Active Point) 등의 개념이 필요합니다.

### 접미사 트리의 응용

- **빠른 문자열 검색**: 패턴 매칭을 선형 시간 내에 수행.
- **중복 서브스트링 찾기**: 가장 긴 반복 부분 문자열 찾기.
- **최대 공통 부분 문자열**: 두 문자열의 최대 공통 부분 문자열 찾기.
- **생물정보학**: DNA 서열 분석 등에서 패턴 탐색.

## 접미사 배열과 접미사 트리의 비교

|특성|접미사 배열|접미사 트리|
|---|---|---|
|**시간 복잡도**|구축: O(n log n), 검색: O(m log n)|구축: O(n), 검색: O(m)|
|**공간 복잡도**|O(n)|O(n) (실제로는 더 큼)|
|**구현의 난이도**|비교적 간단|복잡함|
|**응용 분야**|정렬 기반 문제, 메모리 효율 필요|빠른 검색이 필요한 경우|

- `n`은 문자열의 길이, `m`은 패턴의 길이입니다.
- 접미사 배열은 메모리 사용이 적고 구현이 간단하지만, 검색 속도가 느릴 수 있습니다.
- 접미사 트리는 검색 속도가 빠르지만, 구현이 복잡하고 메모리 사용량이 많습니다.

## 결론

접미사 배열과 접미사 트리는 문자열 알고리즘에서 중요한 도구로, 각각의 장단점이 있습니다. 문제의 특성과 요구사항에 따라 적절한 자료구조를 선택하여 사용하면 효율적인 알고리즘을 구현할 수 있습니다. 접미사 배열은 메모리 효율성과 구현의 간단함이 필요할 때 적합하며, 접미사 트리는 빠른 검색 속도가 필요할 때 유용합니다.