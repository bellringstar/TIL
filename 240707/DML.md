# 데이터 조작어(DML): INSERT, UPDATE, DELETE
DML은 ㄷ이터베이스의 데이터를 조작하는 SQL 명령어들을 포함합니다. 주요 DML 명령어는 INSERT, UPDATE, 
DELETE입니다.

## INSERT
테이블에 새로운 행을 추가합니다.
```sql
INSERT INTO table_name (column1, column2, ...)
VALUES (value1, value2, ...);

INSERT INTO employees (first_name, last_name, email, hier_date)
VALUES ('John', 'Doe', 'john.doe@example.com', '2023-07-01');

INSERT INTO products (product_name, price, category)
VALUES 
('Laptop', 999.99, 'Electronics'),
('Desk Chair', 199.99, 'Furniture'),
('Coffe Mug', 9.99, 'Kitchenware')
```
## UPDATE
테이블의 기존 데이터를 수정합니다.
```sql
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;

UPDATE employees
SET salary = salary * 1.1
WHERE department = 'Sales';
```
WHERE절을 생략하면 테이블의 모든 행이 업데이트 되므로 주의해야 합니다.

### DELETE
테이블에서 행을 삭제합니다.
```sql
DELETE FROM table_name
WHERE condition;

DELETE FROm orders
WHERE order_date < '2020-01-01';
```
DELETE 문 또한 WHERE절을 생략하면 테이블의 모든 데이터가 삭제됩니다 매우 주의해서 사용합시다.

### DML 사용시 주의사항
1. 데이터 무결성: 삽입, 수정, 삭제 작업이 데이터베이스의 무결성 규칙을 위반하지 않도록 주의해야합니다.
2. 트랜잭션: 여러 DML 작업을 하나의 논리적 단위로 처리하려면 트랜잭션을 사용합니다.
3. 백업: 중요한 DML 작업 전에는 항상 데이터를 백업해두는 것이 좋습니다.
