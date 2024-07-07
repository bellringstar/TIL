# SELECT 문과 기본 쿼리 작성
SELECT 문은 데이터베이스에서 데이터를 조회하는 데 사용되는 가장 기본적이고 중요한 SQL 명령어입니다.
기본 SELECT 문의 구조는 다음과 같습니다.
```sql
SELECT column1, column2,..
FROM table_name
WHERE condition
ORDER BY column1, column2, ... ASC|DESC;
```
1. SELECT: 조회할 열을 지정합니다.
   - \* 를 사용하면 모든 열을 선택합니다.
   - 쉼표로 구분하여 여러 열을 선택할 수 있습니다.
   - 열에 별칭을 지정할 수 있습니다. : column_name AS alias_name
2. FROM: 데이터를 가저올 테이블을 지정합니다.
3. WHERE: 조회할 행을 필터링하는 조건을 지정합니다.
    - 비교 연산자: =, <>, <, >, <=, >=
    - 논리 연산자: AND, OR, NOT
    - LIKE: 패턴 매칭에 사용(예: 'A%'는 A로 시작하는 모든 값)
    - IN: 값 목록 중 일치하는 것을 찾음
    - BETWEEN: 범위 내의 값을 찾음
4. ORDER BY: 결과를 정렬합니다.
    - ASC: 오름차순(기본값)
    - DESC: 내립차순
```sql
SELECT * FROM customers; -- 모든 열 선택

SELECT first_name AS 이름, last_name AS 성 FROM customers; -- 특정 열 선택 및 별칭 사용

SELECT product_name, price
FROM products
WHERE category = 'Electronics' AND price < 1000; -- 조건을 사용한 필터링

SELECT employee_name, hire_date
FROM employees
ORDER BY hire_date DESC; -- 정렬

SELECT city
FROM customers
WHERE city LIKE 'New%'; -- 패턴매칭

SELECT product_name, price
FROM products
WHERE category IN ('Books', 'Movies', 'Music'); -- IN

SELECT order_id, order_date
FROM orders
WHERE order_date BETWEEN '2023-01-01' AND '2023-12-31'; -- BETWEEN
```

