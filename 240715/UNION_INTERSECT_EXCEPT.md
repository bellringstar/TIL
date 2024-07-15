# UNION, INTERSECT, EXCEPT
이 연산자들은 여러 SELECT 문의 결과를 결합하거나 비교하는 데 사용됩니다.<br>
MySQL에서는 UNION은 직접 지원하지만, INTERSECT와 EXCEPT는 직접적으로 지원하지 않습니다.

1. UNION
   - 두 개 이상의 SELECT문의 결과를 결합하여 하나의 결과 집합으로 만듭니다.
   - 기본적으로 중복을 제거합니다 (UNION ALL을 사용하면 중복 포함)
   - 각 SELECT 문의 컬럼 수와 데이터 타입이 일치해야 합니다.
   ```sql
    SELECT product_id, product_name FROM products
    WHERE category = 'Electronics'
    UNION 
    SELECT product_id, product_name FROM products
    WHERE price > 1000;
   ```
   - 이 쿼리는 전자제품 카테고리에 속하거나 가격이 1000을 초과하는 모든 제품을 반환합니다.
2. INTERSECT
   - MySQL에서는 직접 지원하지 않지만, INNER JOIN이나 IN을 사용하여 구현할 수 있습니다.
   ```sql
    SELECT product_id, product_name FROM products
    WHERE category = 'Electronics'
    AND product_id IN (
        SELECT product_id FROM products
        WHERE price > 1000
    );
   ```
   - 이 쿼리는 전자제품 카테고리에 속하면서 동시에 가격이 1000을 초과하는 제품을 반환합니다.
3. EXCEPT
   - MySQL에서는 직점 지원하지 않지만, LEFT JOIN이나 NOT IN을 사용하여 구현할 수 있습니다.
   ```sql
    SELECT a.product_id, a.product_name
    FROM (
        SELECT product_id, product_name FROM products
        WHERE category = 'Electronics'
    ) a
    LEFT JOIN (
        SELECT product_id FROM products
        WHERE price > 1000
    ) b ON a.product_id = b.product_id
    WHERE b.product_id IS NULL:
   ```
   - 이 쿼리는 전자제품 카테고리에 속하지만 가격이 1000을 초과하지 않는 제품을 반환합니다.

4. 주의사항
   - UNION을 사용할 때 ORDER BY는 마지막 SELECT 문에서만 사용할 수 있습니다.
     - SQL 표준과 실행 순서
       - SQL 표준에 따르면 UNION 연산은 여러 SELECT 문의 결과를 하나의 결과 집합으로 결합한 후에 정렬이 이루어져야 합니다.
       - 즉, 정렬은 모든 UNION 연산이 완료된 후의 최종 결과에 대해 수행됩니다.
     - 결과 집합의 통합
       - UNION은 여러 SELECT 문의 결과를 하나의 결과 집합으로 통합합니다. 중간 SELECT 문에 ORDER BY를 사용하면 이 통합 과정과 충돌할 수 있습니다.
     - 성능 최적화
       - 데이터베이스 엔진이 UNION 연산을 최적화하기 위해서는 각 SELECT 문의 결과를 자유롭게 처리할 수 있어야 합니다.
       - 중간에 정렬이 발생하면 이러한 최적화가 어려워질 수 있습니다.
     - 명확성과 일관성
       - 마지막 SELECT문에만 ORDER BY를 허용함으로써 전체 UNION 결과에 대한 정렬 의도를 명확하게 표현할 수 있습니다.
   - UNION은 자동으로 중복을 제거하므로 대랴의 데이터르 다룰 때 성능에 영향을 줄 수 있습니다. 필요한 경우 UNION ALL을 사용하여 성능을 개선할 수 있습니다.
   - INTERSECT와 EXCEPT를 구현할 때는 서브쿼리나 조인을 사용하므로, 대용량 데이터에 대해서는 성능을 고려해야 합니다.