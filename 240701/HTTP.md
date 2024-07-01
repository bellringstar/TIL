# HTTP

## HTTP 기본 개념
### HTTP란 무엇인가?
- HTTP(Hypertext Transfer Protocol)는 웹에서 데이터를 주고받기 위한 애플리케이션 계층 프로토콜입니다.
- 클라이언트(주로 웹 브라우저)와 서버 간의 통신 규악을 정의합니다.
- 왜 중요한가?
  - 웹의 기반 기술로, 거의 모든 온라인 활동의 근간이 됩니다.
  - RESTful API, 웹 서비스 등 현대 웹 애플리케이션 개발의 핵심입니다.
### HTTP의 특징
- 클라이언트-서버 모델 : 요청-응답 구조로 동작합니다.
- Stateless(무상태) : 각 요청은 독립적으로 처리됩니다.
- Connectionless(비연결성) : 요청-응답 후 연결을 종료합니다.
- 어떻게 동작하는가?
  - 클라이언트가 서버에 request를 보냅니다.
  - 서버는 요청을 처리하고 response을 반환합니다.
  - 응답 후 연결이 종료됩니다.
  - HTTP 메시지 구조
### Request 메시지 구조
- 시작줄(Start Line) : 메서드, URL, HTTP 버전
- 헤더(Headers) : 요청에 대한 추가 정보
- 비 준(Empty Line) : 헤더의 끝을 나타냄
- 본문(Body) : 전송할 데이터(선택적)
```markdown
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Accept: text/html,application/xhtml+xml
```
### Response 메시지 구조
- 상태줄(Status Line) : HTTP 버전, 상태 코드, 상태 메시지
- 헤더 (Headers) : 응답에 대한 추가 정보
- 빈 줄(Empty Line)
- 본문(Body) : 응답 데이터
```markdown
HTTP/1.1 200 OK
Date: Mon, 23 May 2023 22:38:34 GMT
Content-Type: text/html; charset=UTF-8
Content-Length: 138

<!DOCTYPE html>
<html>
<head>
  <title>Example Page</title>
</head>
<body>
  <h1>Hello, World!</h1>
</body>
</html>
```
- 왜 이런 구조인가?
  - 구조화된 형식으로 정보를 전달하여 해석과 처리를 용이하게 합니다.
  - 확장성을 제공하여 새로운 기능을 쉽게 추가할 수 있습니다.
## HTTP 메서드
- GET : 리소스 조회
- POST : 리소스 생성
- PUT : 리소스 수정
- DELETE : 리소스 삭제
- PATCH : 리소스 부분 수정
- HEAD : 헤더 정보만 조회
- OPTIONS : 지원되는 메서드 조회
- 어째서 이렇게 많은 메서드가 존재할까?
  - RESTful 설계 원칙에 좋다
  - 각 작업의 의도를 명확히 하여 서버 로직 처리를 용이하게 한다.
## HTTP 상태 코드
- 1XX : 정보 응답
- 2XX : 성공
- 3XX : 리다이렉션
- 4XX : 클라이언트 오류
- 5XX : 서버 오류
### 자주 사용되는 상태 코드
- 200 OK : 요청 성공
- 201 Created : 리소스 생성 성공
- 400 Bad Reqeust : 잘못된 요청
- 404 Not Found : 리소스를 찾을 수 없음
- 500 Internal Server Error : 서버 내부 오류

## HTTP 헤더
- General Headers : 요청과 응답 모두에 적용되는 일반 정보 
- Request Headers : 요청에 대한 추가 정보나 조건 설정
- Response Headers : 응답에 대한 추가 정보 제공
- Entity Headers : 메시지 본문에 대한 정보
### 중요 헤더 예시
- Content-Type : 본문의 미디어 타입
- Authorization : 인증 토큰
- Cache-Control : zotld ehdwkr wlwjd
- Cookie/Set-Cookie : 쿠키 정보
### 왜 헤더가 중요한가?
- 메타데이터를 제공하여 HTTP 메시지를 더 잘 이해하고 처리할 수 있게 합니다.
- 보안, 성능 최적화, 세션 관리 등 다양한 기능을 구현할 수 있습니다.
## HTTP/2
### 주요 특징
- 멀티플렉싱 : 하나의 연결로 여러 요청/응답 처리
- 헤더 압축 : HPACK을 사용한 헤더 압축
- 서버 푸시 : 클라이언트 요청 없이 리소스 전송
### 왜 도입되었는가?
- HTTP/1.1의 성능 한계를 극복하기 위해
- 웹 페이지 로딩 속도 향상
## HTTP/3
### 주요 특징
- QUIC 프로토콜 사용
- UDP 기반으로 동작
- 연결 설정 시간 단축
### 왜 중요한가?
- 모바일 환경에서의 성능 개선
- 네트워크 전환 시 연결 유지 용이