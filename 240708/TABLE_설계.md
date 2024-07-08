# 테이블 설계 원칙
## Primary Key 서정
- 각 테이블은 고유한 식별자인 주키를 가져야 합니다.
- 보통 AUTO_INCREMENT 속성을 가진 정수형 컬럼을 사용합니다. 예: id INT, AUTO_INCREMENT PRIMARY KEY

## 적절한 데이터 타입 선택
- 각 컬럼의 데이터 특성에 맞는 타입을 선택합니다.
- 불필요하게 큰 데이터 타입을 피하여 저장 공간을 절약합니다.

## NULL 허용 여부 결정
- 필수 입력 필드는 NOT NULL 제약조건을 사용합니다.
- NULL 값의 의미를 명확히 정의합니다.

## 인덱스 설계
- 자주 검색되는 칼럼에 인덱스를 생성하여 조회 성능을 향상시킵니다.
- 단, 과도한 인덱스는 입력/수정/삭제 성능을 저하시킬 수 있으므로 주의합니다.

## 관계 설정
- 테이블 간의 관계를 외래 키로 정의합니다.
- 참조 무결성을 유지하기 위해 CASCADE, SET NULL 등의 옵션을 적절히 사용하니다.

## 명명 규칙
- 일관된 명명 규칙을 사용하여 가독서을 높입니다.
- 테이블명은 복수형, 컬럼명은 단수형을 사용하는 것이 일반적입니다.

## 정규화
- 데이터 중복을 최소화하고 데이터 일관성을 유지하기 위해 정규화를 적용합니다.

## 데이터 무결성 유지
- CHECK 제약조건을 사용하여 데이터의 유효성을 검사합니다.
- UNIQUE 제약조건으로 중복 데이터를 방지합니다.

```sql
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT NOT NULL,
    order_date DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    total_amount DECIMAL(10, 2) NOT NULL CHECK ( total_amount >= 0 ),
    status ENUM('pending', 'processing', 'shipped', 'delivered') NOT NULL DEFAULT 'pending',
    FOREIGN KEY (customer_id) REFERENCES customers(id) ON DELETE RESTRICT 
);

CREATE INDEX idx_order_date ON orders(order_date);
```
