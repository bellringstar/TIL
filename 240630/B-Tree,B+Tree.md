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
## B+Tree
B-Tree의 변형으로 데이터베이스 인덱싱에 더 최적화된 구조입니다.
### 특징
1. 모든 키가 리프 노드에 저장됩니다.
2. 내부 노드는 키만 저장하고 실제 데이터는 리프 노드에만 저장됩니다.
3. 모든 리프 노드는 링크드 리스트로 연결되어 있어 순차적 접근이 가능합니다.
4. 리프 노드를 제외한 내부 노드의 키는 리프 노드의 키를 복사한 것입니다.
5. 검색, 삽입, 삭제 연산의 시간 복잡도는 모두 O(logN)입니다.
### 장점
- 범위 검색에 매우 효율적입니다.
- 리프 노드에 모든 데이터가 있어 풀 스캔 성능이 좋습니다.
- 내부 노드에 키만 저장되어 더 많은 키를 저장할 수 있어 트리의 높이가 낮아집니다.
### 단점
- 중복된 키가 저장되어 약간의 추가 공간이 필요합니다.
### 원리 및 동작 방식
1. 노드 구조
- 내부 노드 : [key1, pointer1, key2, pointer2 ... , keyn, pointern]
- 리프 노드 : [key1, data1, key2, data2, ..., keyn, datan, next_leaf]
- 리프 노드는 다음 리프 노드를 가리키는 포인터를 추가로 가집니다.
2. 검색 과정
- B-Tree와 유사하지만, 항상 리프노드까지 내려갑니다.
- 리프 노드에서 실제 데이터를 찾습니다.
3. 삽입/삭제 과정
- B-Tree와 유사하지만, 데이터는 항상 리프 노드에만 저장/삭제됩니다.
- 내부 노드의 키 변경은 리프 노드의 첫 번째 키를 복사하는 방식으로 이루어집니다.
```java
public class BPlusTree<K extends Comparable<K>, V> {
    private static final int M = 4; // 노드당 최대 키 수
    private Node root;
    private int height;
    private int n; // 키-값 쌍의 수

    private static class Node {
        private int m;                             // 현재 키의 수
        private Entry[] children = new Entry[M];   // 키-값 쌍의 배열
        private Node next;                         // 다음 리프 노드에 대한 링크 (리프 노드만 해당)

        private Node(int k) {
            m = k;
        }
    }

    private static class Entry {
        private Comparable key;
        private Object val;
        private Node next;     // 다음 레벨의 노드에 대한 링크
    
        public Entry(Comparable key, Object val, Node next) {
          this.key = key;
          this.val = val;
          this.next = next;
        }
    }

    public BPlusTree() {
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
                if (j + 1 == x.m || less(key, children[j + 1].key))
                    return search(children[j].next, key, ht - 1);
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
            for (j = 0; j < h.m; j++) {
                if ((j + 1 == h.m) || less(key, h.children[j + 1].key)) {
                Node u = insert(h.children[j++].next, key, val, ht - 1);
                if (u == null) return null;
                t.key = u.children[0].key;
                t.next = u;
                break;
                }
            }
        }

        for (int i = h.m; i > j; i--)
            h.children[i] = h.children[i - 1];
        h.children[j] = t;
        h.m++;
        if (h.m < M) return null;
        else return split(h, ht);
    }

    private Node split(Node h, int ht) {
        Node t = new Node(M / 2);
        h.m = M / 2;
        for (int j = 0; j < M / 2; j++)
            t.children[j] = h.children[M / 2 + j];
        if (ht == 0) {
            t.next = h.next;
            h.next = t;
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
## B-Tree와 B+Tree의 주요 차이점
1. 데이터 저장 위치
- B-Tree : 모든 노드에 키와 데이터를 저장
- B+Tree : 리프 노드에만 데이터를 저장, 내부 노드는 키만 저장
2. 리프 노드 연결
- B-Tree : 리프 노드 간 연결 없음
- B+Tree : 리프 노드가 링크드 리스트로 연결됨
3. 키 중복
- B-Tree : 키가 한 번만 나타남
- B+Tree : 내부 노드의 키가 리프 노드에도 중복되어 나타남
4. 트리 높이
- B+Tree가 일반적으로 B-Tree보다 높이가 낮음 (내부 노드에 더 많은 키 저장 가능)
5. 범위 검색 성능
- B+Tree가 리프 노드 간 연결로 인해 더 효율적
## 실제 사용 예
- B-Tree : 파일 시스템(NTFS..)
- B+Tree : 대부분의 관계형 데이터베이스 시스템(MySQL의 InnoDB..)
### 성능 특성
- 검색 : 둘 다 O(logN)이지만, B+Tree가 리프 노드에서 순차 접근 가능
- 삽입/삭제 : 둘 다 O(logN)이지만, B+Tree가 리프 노드만 수정하므로 더 효율적일 수 있음
## 기술적 디테일
### 노드 크기 최적화
- 디스크 I/O를 최소화하기 위해 노드 크기는 보통 디스크 블록 크기와 일치시킵니다.
- 노드 크기 = (키 크기 + 포인터 크기) * 최대 자식 수 + 여유 공간
### 캐시 효율성
- B+Tree는 내부 노드에 키만 저장하므로 더 많은 키를 메모리에 캐시할 수 있습니다.
- 이는 디스크 I/O를 줄이고 검색 성능을 향상시킵니다.
### 압축 기법
- 접두사 압축 : 같은 노드 내의 키들 간 공통 접두사를 압축하여 저장 공간을 절약합니다.
- 접미사 잘라내기 : 내부 노드의 키는 구분 용도로만 사용되므로, 최소한의 구분 가능한 접두사만 저장할 수 있습니다.

## 데이터베이스에서의 활용
### 인덱싱
- B+Tree는 대부분의 관계형 데이터베이스의 기본 인덱스 구조로 사용됩니다.
- 클러스터드 인덱스 : 테이블 데이터가 인덱스의 리프 노드에 직접 저장됩니다.
- 논클러스터드 인덱스 : 리프 노드에 실제 데이터 대신 데이터의 위치 포인터를 저장합니다.
### 쿼리 최적화
- 범위 스캔 : B+Tree의 리프 노드 연결 구조를 활용하여 효율적인 범위 쿼리 수행
- 인덱스 커버링 : 쿼리에 필요한 모든 컬럼이 인덱스에 포함된 경우 테이블 접근 없이 인덱스만으로 쿼리 처리
## 다른 데이터베이스 자료 구조
1. 해시 인덱스
- 원리 : 키를 해시 함수로 변환하여 버킷에 저장
- 장점 : 평균 O(1) 시간 복잡도의 점 쿼리 성능
- 단점 : 범위 쿼리에 비효율적, 순서 보존 X
2. Bitmap 인덱스
- 원리 : 각 고유 값에 대해 비트 배열을 사용
- 장점 : 낮은 카디널리티 칼럼에 효율적, 빠른 AND/OR 연산
- 단점 : 높은 카디널리티 칼럼에는 비효율적
3. R-Tree
- 원리 : 다차원 공간 데이터를 인덱싱하기 위한 트리 구조
- 사용 : 지리 정보 시스템(GIS), 공간 데이터베이스
- 장점 : 효율적인 다차원 범위 쿼리
4. Log-Structured Merge Tree(LSM Tree)
- 원리 : 메모리 내 트리와 디스크 상의 여러 레벨의 정렬된 파일을 사용
- 장점 : 쓰기 성능이 매우 좋음, SSD에 최적화
- 단점 : 읽기 성능은 B+Tree보다 다소 낮을 수 있음
- 사용 : NoSQL 데이터베이스(예 : Cassandra, RocksDB)
5. Skip List
- 원리 : 여러 레벨의 링크드 리스트를 사용한 확률적 데이터 구조
- 장점 : 구현이 간단, 동시성 제어가 쉬움
- 단점 : 공간 효율성이 낮음
- 사용 : Redis의 정렬된 집합 구현에 사용

## B+Tree가 B-Tree보다 효율적인 이유
1. 데이터 접근 속도
- B-Tree: 데이터가 내부 노드와 리프 노드 모두에 있어, 검색 시 트리의 어느 레벨에서든 끝날 수 있습니다.
- B+Tree: 모든 데이터가 리프 노드에만 있어, 항상 같은 깊이까지 내려가면 됩니다. 이는 예측 가능한 성능을 제공합니다.
- 왜 효율적인가?
  - 컴퓨터는 예측 가능한 패턴을 더 잘 최적화할 수 있습니다.
2. 캐시 효율성
- B-Tree: 내부 노드에 데이터가 있어 각 노드가 더 큽니다.
- B+Tree: 내부 노드는 키만 가지고 있어 더 작습니다. 따라서 같은 메모리에 더 많은 노드를 저장할 수 있습니다.
- 왜 효율적인가?
  - 더 많은 내부 노드가 메모리에 올라갈 수 있어 디스크 접근 횟수가 줄어듭니다.
3. 범위 검색
- B-Tree: 범위 검색을 위해 트리를 여러 번 순회해야 할 수 있습니다.
- B+Tree: 리프 노드들이 연결되어 있어, 시작점만 찾으면 쉽게 순회할 수 있습니다.
- 왜 효율적인가?
  - 연속된 데이터를 찾을 때 훨씬 빠릅니다. 예를 들어 "A 부터 C로 시작하는 모든 항목"을 찾는 경우 유리합니다.
4. 리프 노드 활용
- B-Tree: 리프 노드와 내부 노드의 구조가 같아, 공간 활용이 덜 효율적일 수 있습니다.
- B+Tree: 리프 노드만 실제 데이터를 저장하므로, 리프 노드를 데이터 저장에 최적화할 수 있습니다.
- 왜 효율적인가?
  -데이터 저장 공간을 더 효율적으로 사용할 수 있습니다.
5. 갱신 연산
- B-Tree: 내부 노드의 데이터도 갱신해야 할 수 있습니다.
- B+Tree: 리프 노드의 데이터만 갱신하면 됩니다.
- 왜 효율적인가?
  - 데이터 갱신 시 영향을 받는 노드가 적어 더 빠릅니다.
### 실제 예시
데이터베이스에서 "2023년 1월 1일부터 2023년 12월 31일까지의 모든 거래 내역"을 조회한다고 가정해봅시다.
- B-Tree: 2023년 1월 1일을 찾고, 그 다음 날짜를 찾기 위해 다시 루트부터 검색을 시작해야 할 수 있습니다.
- B+Tree: 2023년 1월 1일을 찾은 후, 리프 노드들을 따라 쭉 이동하면서 2023년 12월 31일까지 모든 데이터를 순차적으로 읽을 수 있습니다.
<br> 이런 이유들로 B+Tree는 특히 데이터베이스 시스템에서 인덱스 구조로 널리 사용도비니다. 대량의 데이터를 다루면서 빠른 검색과 
범위 쿼리가 자주 필요한 환경에서 B+Tree가 더 효율적인 성능을 보입니다.