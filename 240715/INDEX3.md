# EXPLAIN 명령어 사용법
EXPLAIN은 MySQL에서 쿼리의 실행 계획을 분석하는 강력한 도구이빈다. 이를 통해 쿼리가 어떻게 실행되는지, 어떤 인덱스를 사용하즌지, 테이블을 어떤 순서로 조인하는지 등을 알 수 있습니다.
```sql
EXPLAIN SELECT * FROM users WHERE username = 'john';
```
- EXPLAIN의 주요 출력 컬럼
  - id: SELECT 식별자
  - select_type: SELECT 문의 유형
  - table: 참조되는 테이블
  - partitions: 사용되는 파티션
  - type: 조인 유형
  - possible_keys: 사용 가능한 인덱스 목록
  - key: 실제 사용된 인덱스
  - key_len: 사용된 인덱스의 길이
  - ref: 인덱스와 비교되는 컬럼
  - rows: 검사할 예상 행 수
  - filtered: 필터링된 행의 백분율
  - Extra: 추가 정보

- 주요 항목
  - type
    - 성능 순으로 system > const > eq_ref > ref > range> index > ALL
    - 'ALL'은 전체 테이블 스캔을 의미하며 가장 비효율적
    - 'const', 'eq_ref'는 매우 효율적인 접근 방식
  - key
    - 실제 사용된 인덱스, NULL이면 인덱스를 사용하지 않음
  - rows
    - MySQL이 쿼리를 처리하기 위해 검사해야 할 것으로 예상하는 행의 수
    - 이 값이 크면 쿼리가 비효율적일 가능성이 높음
  - Extra
    - Using index: 커버링 인덱스 사용(매우 효율적)
    - Using where: WHERE절 사용
    - Using temporary: 임시 테이블 사용(비효율)
    - Using filesort: 파일 정렬 사용(비효율)