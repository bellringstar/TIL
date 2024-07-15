# JOIN 유형 및 사용법
조인은 두 개 이상의 테이블에서 관련된 행들을 결합하는 연산입니다.

1. INNER JOIN
   - 두 테이블에서 조인 조건을 만족하는 행만 반환합니다.
   - 참고로 열 이름을 명확히 하지 않는 경우 동일열이 존재한다면 FROM절의 테이블이 우선순위를 가진다.
```sql
SELECT customers.name, orders.order_date
FROM custumers
INNER JOIN orders ON customers.id = orders.customer_id;
```
2. LEFT JOIN(또는 LEFT OUTER JOIN)
   - 왼쪽 테이블의 모든 행과 오른쪽 테이블에서 조건을 만족하는 행을 반환합니다.
```sql
SELECT customers.name, orders.order_date
FROM customers
LEFT JOIN orders ON customers.id = orders.customer_id;
```
3. RIGHT JOIN(또는 RIGHT OUTER JOIN)
   - 오른쪽 테이블의 모든 행과 왼쪽 테이블에서 조건을 만족하는 행을 반환합니다.
```sql
SELECT customers.name, orders.order_date
FROM customers
RIGHT JOIN orders ON customers.id = orders.customer_id;
```
4. FULL JOIN(MySQL에서는 직접 지원하지는 않지만 LEFT JOIN과 UNION을 사용해 구현 가능)
   - 양쪽 테이블의 모든 행을 반환합니다.
```sql
SELECT customers.name, orders.order_date
FROM customers
LEFT JOIN orders ON customers.id = orders.customer_id
UNION 
SELECT customers.name, orders.order_data
FROM customers
RIGHT JOIN orders ON customers_id = orders.customer_id
WHERE customers.id IS NULL;
```
5. CROSS JOIN
   - 두 테이블의 모든 가능한 조합을 반환합니다.
```sql
SELECT customers.name, products.product_name
FROM customers
CROSS JOIN products;
```
