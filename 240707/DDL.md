# 데이터 정의어 DDL: CREATE, ALTER, DROP
DDL은 데이터베이스 구조를 정의하고 관리하는 SQL 명령어들을 포함합니다. 주요 DDL 명령어는
CREATE, ALTER, DROP입니다.

## 데이터베이스 객체란?
이런 DDL들은 데이터베이스 객체를 대상으로 동작합니다. 데이터베이스 객체란 무엇일까요?<br>
데이터베이스 객체는 데이터베이스 내에서 데이터를 저장하고 관리하는데 사용되는 구조화된 요소들입니다.
### 데이터베이스(Database)
- 관련된 테이블과 다른 객체들의 집합입니다.
- 하나의 MySQL 서버는 여러 개의 데이터베이스를 가질 수 있습니다.
### 테이블(Table)
- 행과 열로 구성된 2차원 구조로, 데이터를 저장하는 기본 단위입니다.
- 각 열은 특정 데이터 타입을 가집니다.
### 뷰(View)
- 하나 이상의 테이블에서 파생된 가상 테이블입니다.
- 복잡한 쿼리를 단순화하거나 데이터 접근을 제한하는 데 사용됩니다.
### 인덱스(Index)
- 데이터 검색 속도를 향상시키기 위한 데이터 구조입니다.
- 특정 열에 대해 생성되며, 해당 열의 값을 빠르게 찾을 수 있게 합니다.
### 저장 프로시저(Stored Procedure)
- 데이터베이스에 저장되는 SQL문의 집합입니다.
- 복잡한 로직을 캡슐화하고 재사용할 수 있게 해줍니다.
### 함수(Function)
- 특정 계산이나 작업을 수행하고 결과를 반환하는 코드 블록입니다.
- SQL 쿼리 내에서 호출하여 사용할 수 있습니다.
### 시퀀스(Sequence)
- 순차적인 고유 숫자를 생성하는 데 사용되는 객체입니다.
- MySQL 8.0부터 지원됩니다.
### 이벤트(Event)
- 정해진 일정에 따라 자동으로 실행되는 테스크입니다.
- 데이터베이스 유지보수나 주기적인 데이터 처리에 사용됩니다.
## CREATE
새로운 데이터베이스 객체(데이터베이스, 테이블, 인덱스 등)를 생성합니다.
```sql
CREATE DATABASE database_name;

CREATE TABLE table_name (
    column1 datatype constraints,
    column2 datatype constraints,
    ....
    PRIMARY KEY (column)
);

CREATE TABLE employees (
    id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE,
    hire_date DATE,
    salary DECIMAL(10, 2)
);
```

## ALTER
기존 데이터베이스 객체의 구조를 수정합니다.
```sql
ALTER TABLE table_name
ADD COLUMN column_name datatype;

ALTER TABLE table_name
DROP COLUMN column_name;
     
ALTER TABLE table_name
MODIFY COLUMN column_name new_datatype;

ALTER TABLE employees
ADD COLUMN department VARCHAR(50);

ALTER TABLE employees
MODIFY COLUMN salary DECIMAL(12, 2);
```

## DROP
데이터베이스 객체를 삭제합니다.
```sql
DROP TABLE table_name;

DROP DATABASE database_name;
```
DROP은 되돌릴 수 없어요...

## DDL 사용 시 주의사항
1. 백업: 중요한 DDL 작업 전에는 반드시 백업!
2. 권한: DDL 명령어는 데이터베이스 구조를 변경하므로, 적절한 권한이 있는 사용자만 실행해야 합니다.
3. 성능 영향: 대규모 테이블에 대한 DDL 작업은 데이터베이스 성능에 큰 영향을 줄 수 있으므로 가능한 피크 시간을 피해 실행해야 합니다.