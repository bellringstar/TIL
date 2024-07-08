# MySQL 데이터 타입

## 숫자 타입
- INT: 정수 (-2,147,483,648 to 2,147,483,647)
- BIGINT: 큰 정수
- FLOAT: 단정밀도 부동소수점
- DOUBLE: 배정밀도 부동소수점
- DECIMAL: 정확한 십진수

## 문자열 타입
- CHAR(n): 고정 길이 무자열
- VARCHAR(n): 가변 길이 문자열
- TEXT: 긴 텍스트 데이터

## 날짜 및 시간 타입
- DATE: YYYY-MM-DD 형식의 날짜
- TIME: HH:MM:SS 형식의 시간
- DATETIME: YYYY-MM-DD HH:MM:SS 형식의 날짜와 시간
- TIMESTAMP: DATETIME과 유사하지만 시단대 변환 지원

## 이진 데이터 타입
- BLOB: 큰 이진 객체

## 기타 타입
- ENUM: 미리 정의된 값 목록 중 하나를 선택
- SET: 미리 정의도니 값 목록 중 여러 개를 선택

```sql
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10, 2),
    description TEXT,
    creatd_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```