# B-Tree,B+Tree

## B-Tree (Balanced Tree)
B-Tree는 이진 트리를 일반화한 균형 트리 구조입니다.
### 특징
1. 모든 리프 노드는 같은 레벨에 있습니다.
2. 루트를 제외한 모든 노드는 최소 m/2 - 1개에서 최대 m-1개의 키를 가집니다.(m은 B-Tree의 차수)
3. 각 노드의 키는 오름차순으로 정렬되어 있습니다.
4. 각 내부 노드는 n개의 키를 가지고 있다면 n+1개의 자식을 가집니다.
5. 검색, 삽입, 삭제 연산의 시간 복잡도는 모두 O(logN)입니다.
### 장점
- 균형 잡힌 구조로 안정적인 성능을 보장합니다.
- 범위 검색에 효율적입니다.
### 단점
- 노드 내에 키와 데이터가 함께 저장되어 메모리 사용량의 높을 수 있습니다.
### 원리 및 동작 방식
1. 노드 구조
- 각 노드는 [key1, pointer1, key2, pointer2,..., keyn, pointern] 형태를 가집니다.
- 키는 정렬된 순서로 저장됩니다.
- 포인터는 자식 노드를 가리킵니다.
2. 검색 과정
- 루트에서 시작하여 키를 비교하며 하양식으로 탐색합니다.
- 각 노드에서 key[i-1] < serach_key <= key[i] 를 만족하는 포인터를 따라 이동합니다.
3. 삽입 과정
- 적절한 리프 노드를 찾아 키를 삽입합니다.
- 노드가 가득 차면 분할(split)이 발생합니다.
  - 중간 키를 부모 노드로 올립니다.
  - 나머지 키들로 새 노드를 만듭니다.
- 필요시 루트까지 분할이 전파됩니다.
4. 삭제 과정
- 키를 찾아 삭제합니다.
- 노드의 키 수가 최소 요구치 미만이 되면
  - 형제 노드에서 키를 빌려옵니다.(재분배)
  - 또는 형제 노드와 병합합니다.
- 필요시 루트까지 변경이 전파됩니다.
```java
public class BTree<K extends Comparable<K>, V> {
    private static final int M = 4; // 노드당 최대 자식 수
    private Node root;
    private int height;
    private int n; // 키-값 쌍의 수
    
    public static class Node {
        private int m; // 현재 자식/키의 수
        private Entry[] children = new Entry[M]; // 키-값 쌍의 배열

        private Node(int k) {
            m = k;
        }
    }
    
    private static class Entry {
        private Comparable key;
        private Object val;
        private Node next; // 다음 레벨의 노드에 대한 링크
        
        public Entry(Comparable key, Object val, Node next) {
            this.key = key;
            this.val = val;
            this.next = next;
        }
    }
    
    public BTree() {
        root = new Node(0);
    }
    
    public V get(K key) {
        return search(root, key, height);
    }
    
    @SuppressWarnings("unchecked")
    private V search(Node x, K key, int ht) {
        Entry[] children = x.children;
        
        if (ht == 0) {
            for (int j = 0; j < x.m; j++) {
                if (eq(key, children[j].key)) return (V) children[j].val;
            }
        } else {
            for (int j = 0; j < x.m; j++) {
                if (j + 1 == x.m || less(key, children[j + 1].key)) {
                    return search(children[j].next, key, ht - 1);
                }
            }
        }
        return null;
    }

    public void put(K key, V val) {
        Node u = insert(root, key, val, height);
        n++;
        if (u == null) return;
        
        Node t = new Node(2);
        t.children[0] = new Entry(root.children[0].key, null, root);
        t.children[1] = new Entry(u.children[0].key, null, u);
        root = t;
        height++;
    }
    
    private Node insert(Node h, K key, V val, int ht) {
        int j;
        Entry t = new Entry(key, val, null);

        if (ht == 0) {
            for (j = 0; j < h.m; j++) {
                if (less(key, h.children[j].key)) break;
            }
        } else {
            for (int j = 0; j < h.m; j++) {
                if ((j + 1 == h.m) || less(key, h.children[j + 1].key)) {
                    Node u = insert(h.children[j++].next, key, val, ht - 1);
                    if (u == null) return null;
                    t.key = u.children[0].key;
                    t.next = u;
                    break;
                }
            }
        }

        for (int i = h.m; i > j ; i--) {
            h.children[i] = h.children[i - 1];
            h.children[j] = t;
            h.m++;
            if (h.m < M) return null;
            else return split(h);
        }
    }
    
    private Node split(Node h) {
        Node t = new Node(M / 2);
        h.m = M / 2;
        for (int j = 0; j < M / 2; j++) {
            t.children[j] = h.children[M / 2 + j];
        }
        return t;
    }

    private boolean less(Comparable k1, Comparable k2) {
        return k1.compareTo(k2) < 0;
    }
    
    private boolean eq(Comparable k1, Comparable k2) {
        return k1.compareTo(k2) == 0;
    }
}
```
## B+Tree는 B-Tree의 변형으로 데이터베이스 인덱싱에 더 최적화된 구조입니다.