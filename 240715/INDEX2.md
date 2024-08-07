# 인덱스 생성 및 관리

1. 인덱스 생성
```sql
CREATE INDEX index_name ON table_name (column1, column2, ...);

CREATE INDEX idx_lastname ON customers (last_name);

CREATE INDEX idx_name ON customers (last_name, first_name);

CREATE UNIQUE INDEX idx_email ON users (email);

DROP INDEX index_name ON table_name;

SHOW INDEX FROM table_name;
```
2. 인덱스 관리 시 고려사항
   - 인덱스 선택성
     - 선택성이 높은 컬럼(고유한 값이 많은 컬럼)에 인덱스를 생성하는 것이 효과적입니다.
     - 예: 성별보다는 주민등록번호가 더 좋은 인덱스 대상입니다.
   - 복합 인덱스 순서
     - 가장 자주 사용되는 컬럼을 앞에 위치시킵니다.
     - 선택성이 높은 컬럼을 앞에 위치시킵니다.
     - 복합 인덱스에서 순서가 중요한 이유는 "왼쪽 접두사 규칙" 때문입니다.
       - 인덱스는 정렬된 상태로 저장되며, 왼쪽에서 오른쪽으로 순차적으로 정렬됩니다.
       - 쿼리에서 인덱스의 왼쪽 컬럼부터 순서대로 사용해야 인덱스가 효과적으로 작동합니다.
   - 인덱스 크기
     - 인덱스도 저장 공간을 차지하므로, 필요 이상으로 많은 인덱스를 생성하지 않도록 합니다.
   - 갱신 빈도
     - 자주 갱신되는 컬럼에 인덱스를 생성하면 성능 저하가 발생할 수 있습니다.
   - 인덱스 재구성
     - 시간이 지나면서 인덱스 성능이 저하될 수 있으므로, 주기적으로 인덱스를 재구성할 필요가 있습니다.
     - 저하의 주요 원인은 인덱스 단편화입니다.
       - 페이지 분할: 새 데이터가 삽입되면서 인덱스 페이지가 가득 차면, 페이지 분할이 발생합니다. 이로 인해 인덱스 구조가 비효율적으로 변할 수 있습니다.
       - 삭제 공간: 데이터가 삭제되면 인덱스 페이지에 빈 공간이 생기지만, 이 공간이 항상 효율적으로 재사용되지는 않습니다.
       - 논리적 순서와 물리적 순서의 불일치: 시간이 지나면서 데이터의 논리적 순서와 실제 저장된 물리적 순서가 크게 달라질 수 있습니다.
   - 커버링 인덱스
     - 쿼리에서 필요한모든 컬럼을 포함하는 인덱스를 생성하면, 테이블 접근 없이 인덱스만으로 쿼리를 처리할 수 있습니다.
   - 인덱스 힌트
     - 옵티마이저의 판단과 다르게 특정 인덱스를 사용하고 싶을 때 힌트를 사용할 수 있습니다.
   - 불필요 인덱스 제거
     - 사용되지 않는 인덱스는 제거하여 시스템 리소스를 절약합니다.