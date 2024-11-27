**트라이(Trie)**는 문자열이나 시퀀스를 효율적으로 저장하고 검색하기 위한 트리 기반의 자료구조입니다. 특히, 문자열의 집합에서 특정 패턴이나 키의 검색, 자동 완성, 사전(Dictionary) 구현 등에 유용하게 사용됩니다.

## 개념

- **노드(Node)**: 각 노드는 하나의 문자 또는 키의 부분을 나타냅니다.
- **루트(Root)**: 빈 문자열 또는 공통 접두사를 나타내는 노드입니다.
- **경로(Path)**: 루트에서부터 특정 노드까지의 경로는 하나의 문자열이나 키를 나타냅니다.
- **단어의 끝 표시**: 특정 노드가 문자열의 끝임을 표시하기 위해 플래그를 사용합니다.

## 특징

- **효율적인 검색**: 문자열의 길이에 비례하는 O(m)의 시간 복잡도로 검색이 가능합니다. (m은 검색할 문자열의 길이)
- **공간 효율성**: 공통된 접두사를 공유하므로 저장 공간을 절약할 수 있습니다.
- **알파벳 크기에 영향**: 각 노드에서 자식 노드에 대한 포인터를 알파벳의 크기(예: 26개 영문자)에 따라 가집니다.

## 적용 분야

- **자동 완성 기능**
- **검색 엔진의 검색어 추천**
- **사전(Dictionary) 구현**
- **IP 라우팅**
- **문자열 통계 및 분석**

## 트라이의 주요 연산

### 1. 삽입(Insertion)

- 문자열의 각 문자를 따라 트리를 내려가면서 노드를 생성하거나 이동합니다.
- 문자열의 끝에 도달하면 해당 노드에 단어의 끝임을 표시합니다.

### 2. 검색(Search)

- 문자열의 각 문자를 따라 트리를 내려갑니다.
- 문자열의 끝에 도달하면 해당 노드의 단어 끝 표시를 확인하여 존재 여부를 반환합니다.

### 3. 삭제(Deletion)

- 문자열을 검색하면서 노드를 따라 내려갑니다.
- 문자열의 끝에 도달하면 단어 끝 표시를 제거합니다.
- 필요에 따라 자식이 없는 노드를 삭제하여 트리를 정리합니다.

## 자바로 구현하기

### 트라이 노드 클래스 구현
```Java
import java.util.HashMap;
import java.util.Map;

public class TrieNode {
    Map<Character, TrieNode> children;
    boolean isEndOfWord;

    // 생성자
    public TrieNode() {
        children = new HashMap<>();
        isEndOfWord = false;
    }
}

```
### ### 트라이 클래스 구현
```Java
public class Trie {
    private TrieNode root;

    // 생성자
    public Trie() {
        root = new TrieNode();
    }

    // 삽입 메서드
    public void insert(String word) {
        TrieNode node = root;
        for (char ch : word.toCharArray()) {
            // 자식 노드 중 해당 문자가 없으면 새로 생성
            node.children.putIfAbsent(ch, new TrieNode());
            node = node.children.get(ch);
        }
        // 단어의 끝 표시
        node.isEndOfWord = true;
    }

    // 검색 메서드
    public boolean search(String word) {
        TrieNode node = root;
        for (char ch : word.toCharArray()) {
            // 자식 노드에 해당 문자가 없으면 false 반환
            if (!node.children.containsKey(ch)) {
                return false;
            }
            node = node.children.get(ch);
        }
        // 단어의 끝인지 확인
        return node.isEndOfWord;
    }

    // 접두사 검색 메서드
    public boolean startsWith(String prefix) {
        TrieNode node = root;
        for (char ch : prefix.toCharArray()) {
            // 자식 노드에 해당 문자가 없으면 false 반환
            if (!node.children.containsKey(ch)) {
                return false;
            }
            node = node.children.get(ch);
        }
        return true;
    }

    // 삭제 메서드
    public void delete(String word) {
        delete(root, word, 0);
    }

    private boolean delete(TrieNode current, String word, int index) {
        if (index == word.length()) {
            // 단어의 끝인 경우
            if (!current.isEndOfWord) {
                return false;
            }
            current.isEndOfWord = false;
            // 자식 노드가 없으면 삭제 가능
            return current.children.isEmpty();
        }
        char ch = word.charAt(index);
        TrieNode node = current.children.get(ch);
        if (node == null) {
            return false;
        }
        boolean shouldDeleteCurrentNode = delete(node, word, index + 1);

        // 자식 노드를 삭제해야 하는 경우
        if (shouldDeleteCurrentNode) {
            current.children.remove(ch);
            // 현재 노드가 단어의 끝이 아니고 자식이 없으면 삭제 가능
            return current.children.isEmpty() && !current.isEndOfWord;
        }
        return false;
    }
}

public class Main {
    public static void main(String[] args) {
        Trie trie = new Trie();

        // 단어 삽입
        trie.insert("apple");
        trie.insert("app");
        trie.insert("application");

        // 단어 검색
        System.out.println("Search 'apple': " + trie.search("apple")); // true
        System.out.println("Search 'app': " + trie.search("app")); // true
        System.out.println("Search 'apricot': " + trie.search("apricot")); // false

        // 접두사 검색
        System.out.println("StartsWith 'ap': " + trie.startsWith("ap")); // true
        System.out.println("StartsWith 'bat': " + trie.startsWith("bat")); // false

        // 단어 삭제
        trie.delete("app");
        System.out.println("Search 'app' after deletion: " + trie.search("app")); // false
        System.out.println("Search 'apple' after deletion of 'app': " + trie.search("apple")); // true
    }
}

```

### 코드 설명

- **`TrieNode` 클래스**: 각 노드를 구현하며, 자식 노드들을 저장하기 위한 `children` 맵과 단어의 끝임을 나타내는 `isEndOfWord` 불리언 변수를 가집니다.
- **`Trie` 클래스**: 트라이 자료구조를 구현하며, 삽입, 검색, 접두사 검색, 삭제 메서드를 제공합니다.
- **`insert` 메서드**: 단어의 각 문자를 따라 자식 노드를 생성하거나 이동하며, 단어의 끝에 `isEndOfWord`를 `true`로 설정합니다.
- **`search` 메서드**: 단어의 각 문자를 따라 자식 노드를 탐색하며, 단어의 끝에 도달했을 때 `isEndOfWord`를 확인하여 존재 여부를 반환합니다.
- **`startsWith` 메서드**: 주어진 접두사가 트라이에 존재하는지 확인합니다.
- **`delete` 메서드**: 재귀적으로 노드를 탐색하여 단어를 삭제하며, 필요에 따라 노드를 제거하여 트리를 정리합니다.
- **`Main` 클래스**: 트라이를 생성하고 단어를 삽입, 검색, 삭제하는 예제를 보여줍니다.

## 트라이의 시간 복잡도

- **삽입, 검색, 삭제**: O(m), m은 단어의 길이
- **공간 복잡도**: O(N * M), N은 저장된 단어의 수, M은 단어의 평균 길이

## 트라이 사용 시 고려사항

- **공간 효율성**: 알파벳의 크기(예: 영어 소문자 26개)에 따라 각 노드가 많은 자식에 대한 포인터를 가질 수 있으므로, 공간 사용량이 클 수 있습니다.
- **메모리 최적화**: 필요한 경우 컴팩트 트라이, 라디کس 트리(Radix Tree) 등을 사용하여 메모리 사용을 최적화할 수 있습니다.
- **유니코드 지원**: 다양한 문자를 지원하기 위해 `HashMap`을 사용하여 자식 노드를 관리하면 유연성을 높일 수 있습니다.

## 트라이의 응용 분야

- **자동 완성 및 추천 시스템**: 사용자가 입력한 접두사에 따라 단어를 자동으로 완성하거나 추천합니다.
- **문자열 통계**: 특정 패턴이나 단어의 출현 빈도를 효율적으로 계산할 수 있습니다.
- **IP 라우팅**: 네트워크에서 IP 주소를 기반으로 패킷을 라우팅할 때 사용됩니다.
- **DNA 서열 분석**: 생물정보학에서 DNA 서열의 패턴을 찾거나 비교할 때 활용됩니다.

## 결론

트라이는 문자열 집합을 효율적으로 저장하고 검색할 수 있는 강력한 자료구조입니다. 특히, 문자열 검색과 자동 완성 기능이 필요한 응용 프로그램에서 유용하게 사용됩니다. 트라이의 기본 개념과 구현 방법을 이해하면 다양한 문제 해결에 응용할 수 있으며, 필요에 따라 변형된 트라이 구조를 사용하여 성능을 최적화할 수 있습니다.