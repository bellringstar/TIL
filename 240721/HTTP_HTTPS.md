# HTTP와 HTTPS

## HTTP
- 특징
  - 클라이언트와 서버 간의 통신에 사용됩니다.
  - 평문(텍스트) 데이터를 주고 받습니다.
  - 기본적으로 80번 포트를 사용합니다.
  - 상태를 저장하지 않는(stateless) 프로토콜입니다.

## HTTPS
- 특징
  - HTTP에 SSL/TLS(Secure Sockets Layer/Transport Layer Security) 프로토콜을 추가하여 암호화된 통신을 제공합니다.
  - 기본적으로 443번 포트를 사용합니다.
  - 데이터의 기밀성과 무결성을 보장합니다.

## HTTP, HTTPS 차이점
- 보안성
  - HTTP: 데이터가 평문으로 전송되어 중간자 공격에 취약합니다.
  - HTTPS: 데이터가 암호화되어 전송되므로 보안성이 높습니다.
    - HTTP의 데이터 전송 방법
      - 클라이언트-서버 모델
        - HTTP는 클라이언트-서버 모델을 기반으로 합니다. 클라이언트(주로 웹 브라우저)가 요청을 보내면 서버가 응답합니다.
        - 동작 원리
          - 연결 설정
            - 클라이언트가 서버에 연결을 요청(TCP 3-way handshake)
            - 서버가 연결을 수락하고 소켓(endpoint)를 생성
          - 요청 전송
            - 클라이언트가 서버에 요청을 전송
            - HTTP의 경우, 요청 메소드, URI, 헤더, 본문 등을 포함
          - 요청 처리
            - 서버가 요청을 수신하고 해석
            - 필요한 작업을 수행
          - 응답 전송
            - 서버가 처리 결과를 응답으로 클라이언트에게 전송
            - HTTP의 경우 상태 코드, 헤더, 본문 등을 포함
          - 연결 종료
            - 응답 전송 후 연결을 종료하거나 유지(Keep-Alive)
          - 기술적 특징
            - 비동기 통신
              - 클라이언트는 요청을 보낸 후 다른 작업을 수행할 수 있음
              - 서버는 여러 클라이언트의 요청을 동시에 처리 가능
            - 상태 관리
              - HTTP는 기본적으로 stateless이지만, 쿠키나 세션을 통해 상태 유지 가능
              - RESTful API에서는 상태를 클라이언트에서 관리하는 것을 권장
            - 로드 밸런싱
              - 여러 서버를 사용하여 부하를 분산
              - 라운드 로빈, 최소 연결 수 등의 알고리즘 사용
            - 캐싱
              - 클라이언트와 서버 양쪽에서 데이터를 캐싱하여 성능 향상
              - HTTP의 ETag, Last-Modified 등의 헤더를 활용
            - 보안
              - SSL/TLS를 사용한 암호화 통신
              - 인증 및 인가 메커니즘 구현(예: OAuth, JWT)
          - 장단점
            - 장점
              - 역할의 명확한 분리로 유지보수가 용이
              - 중앙 집중 식 데이터 관리로 데이터 일관성 유지
              - 확장성이 뛰어남
            - 단점
              - 서버가 단일 실패 지점이 될 수 있음
              - 네트워크 지연이 발생할 수 있음
              - 서버 리소스 관리가 복잡할 수 있음.
      - 요청-응답 구조
        - 항상 클라이언트의 요청으로 시작되며, 서버의 응답으로 끝납니다.
      - 메시지 형식
        - 헤더: 메타데이터를 포함
        - 바디: 실제 데이터를 포함 (선택적)
        - 메시지의 기본 구조
          - 리퀘스트와 리스폰스 두 유형 모두 기본 구조가 같습니다.
            - Start Line
            - Headers
            - Empty Line
            - Message Body
        - 요청 메시지
          - 시작 라인
            - 메소드 URI HTTP버전
            - 예: GET /index.html HTTP/1.1
          - 헤더
            - 키-값 쌍으로 구성(Key:Value)
            - 예: Host: www.example.com
          - 빈줄: 헤더의 끝을 나타냄
          - 본문(선택적)
            - POST, PUT등의 메서드에서 주로 사용
        - 응답 메시지
          - 상태 라인
            - HTTP버전 상태코드 상태텍스트
            - 예: HTTP/1.1 200 OK
          - 헤더
            - 키-값 쌍으로 구성
            - 예: Content-Type: text/html
          - 빈줄
          - 본문
            - 요청된 리소스의 내용
        - 헤더의 기술적 세부사항
          - 헤더 분류
            - General Headers: 요청과 응답 모두에 적용
            - Request Headers: 요청에만 적용
            - Response Headers: 응답에만 적용
            - Entity Headers: 메시지 본문에 대한 정보
          - 주요 헤더 예시 및 기능
            - Content-Type: 본문의 미디어 타입 지정
            - Content-Length: 본문의 길이(바이트)
            - Accept: 클라이언트가 받아들일 수 있는 미디어 타입
            - User-Agent: 클라이언트 소프트웨어 정보
            - Cache-Control: 캐싱 동작 지정
            - Authorization: 인증 토큰 전달
          - 사용자 정의 헤더
            - 'X-'접두사를 사용하여 비표준 헤더 정의 가능 (현재는 'X-' 접두사 비권장)
              - 표준화 문제
                - 많은 'X-'헤더가 사실상 표준이 되었지만, 접두사 때문에 공식 표준화가 어려워짐
              - 네임스페이스 오염
                - 무분별한 사용으로 헤더 네임스페이스가 복잡해짐
              - 보안 문제
              - 호환성 문제
            - 예: X-Request-ID: 12345
        - 메시지 본문 인코딩
          - Content-Encoding
            - gzip, deflate 등의 압축 알고리즘 사용
            - 네트워크 데역폭 절약
          - Transfer-Encoding
            - chunked: 큰 데이터를 청크 단위로 분할 전송
            - 동적 콘텐츠 생성 시 유용
          - Multipart
            - 여러 개의 메시지를 하나의 본문에 포함
            - 파일 업로드 등에 사용
        - 메시지 파싱 및 생성
          - 파싱 과정
            - 시작 라인 파싱
            - 헤더 파싱 (키-값 분리)
            - 빈 줄 확인
            - 본문 파싱 (Content-Length 또는 Transfer-Encoding에 따라)
          - 생성 과정
            - 시작 라인 생성
            - 필요한 헤더 추가
            - 빈 줄 추가
            - 본문 생성 및 추가
        - HTTP/2와 HTTP3에서의 변화
          - HTTP/2
            - 바이너리 프로토콜로 변경
            - 헤더 압축 (HPACK) 도입
            - 스트림 기반 통신
          - HTTP/3
            - QUIC 프로토콜 사용
            - UDP 기반으로 변경
            - 연결 설정 시간 단축
      - HTTP 메소드
        - GET: 리소스 조회
          - 멱등성 O
          - 안전성 O
          - 캐시 가능 O
          - URL에 쿼리 파라미터로 데이터 전달
          - 브라우저 히스토리에 남음
          - 북마크 가능
        - POST: 새로운 리소스 생성, 프로세스 처리 등
          - 멱등성 X
          - 안전성 X
          - 캐시 가능: 응답에 따라 다름
          - 요청 본문 O
          - 요청 데이터 크기에 제한 없음
          - 여러 번 호출 시 여러 리소스 생성 가능
        - PUT: 리소스 생성 또는 완전한 대체
          - 멱등성 O
          - 안전성 X
          - 캐시 가능 X
          - 요청 본문 O
          - 클라이언트가 리소스 위치르 알고 있어야 함
          - 같은 요청을 여러 번 보내도 결과는 동일
        - DELETE: 리소스 삭제 등
          - 멱등성 O
          - 안전성 X
          - 캐시 가능 X
          - 요청 본문: 일반적으로 없음
          - 성공시 200(OK) 또는 204(No Content) 반환
          - 리소스가 이미 없는 경우 404(Not Found) 반환 가능
        - PATCH: 리소스 부분 수정
          - 멱등성 X
          - 안전성 X
          - 캐시 가능 X
          - 요청 본문 O
          - 기술적 특징
            - 변경할 필드만 전송 가능
            - PATCH 요청의 형식은 서버와 클라이언트 간 약속 필요
        - HEAD: GET과 동일하지만 응답 본문을 받지 않음
          - 멱등성 O
          - 안전성 O
          - 캐시 가능 O
          - 리소스 존재 여부, 헤더 정보만 필요할 때 사용
          - 대역폭 절약에 유용
        - OPTIONS: 리소스에 대한 통신 옵션 확인
          - 멱등성 O
          - 안전성 O
          - 캐시 가능 X
          - CORS(Cross-Origin Resource Sharing)에서 사용
          - 서버가 지원하는 메소드, 헤더 등 정보를 제공
        - 메소드의 특성
          - 멱등성
            - 동일한 요청을 여러 번 보내도 결과가 같음
            - GET, PUT, DELETE는 멱등, POST는 멱등하지 않음
            - 네트워크 오류 시 재시도 전략에 중요
          - 안전성
            - 리소스를 변경하지 않는 특성
            - GET, HEAD, OPTIONS는 안전한 메소드
          - 캐시 가능성
            - 응답을 캐싱할 수 있는지 여부
            - GET, HEAD는 기본적으로 캐시 가능
            - RESTful API 설계에서의 메소드 사용
          - 리소스 중심 설계
            - URL은 리소스를 나타내고, 메소드는 행위를 나타냄
          - HATEOAS(Hypermedia as the Engine of Application State)
            - 응답에 관련 리소스의 링크 포함
            - 클라이언트가 API를 동적으로 탐색 가능
        - 확장 메소드
          - TRACE: 요청 메시지 루프백 테스트
          - CONNECT: 프록시 서버와의 터널링 설정
      - 상태 코드
        - 구조
          - 1xx: 정보 응답
          - 2xx: 성공 응답
          - 3xx: 리다이렉션
          - 4xx: 클라이언트 오류
          - 5xx: 서버 오류
        - 주요 상태코드
          - 1xx (정보)
            - 100 Continue: 클라이언트가 계속 요청을 보내도 됨
            - 101 Switching Protocols: 프로토콜 전환 (예: WebSocket)
          - 2xx (성공)
            - 200 OK: 요청 성공
            - 201 Created: 리소스 생성 성공 (주로 POST 요청 후)
            - 204 No Content: 성공은 했지만 응답 본문 없음
            - 206 Partial Content: 부분적 GET 요청 성공
          - 3xx (리다이렉션)
            - 301 Moved Permanently: 리소스가 영구적으로 이동
            - 302 Found: 리소스의 임시 이동
            - 304 Not Modified: 캐시된 리소스가 여전히 유효함
            - 307 Temporary Redirect: 302와 유사하나 HTTP 메소드 변경 불가
            - 308 Permanent Redirect: 301과 유사하나 HTTP 메소드 변경 불가
          - 4xx (클라이언트 오류)
            - 400 Bad Request: 잘못된 문법 등으로 서버가 요청을 이해할 수 없음
            - 401 Unauthorized: 인증 필요
            - 403 Forbidden: 권한 부족
            - 404 Not Found: 리소스를 찾을 수 없음
            - 405 Method Not Allowed: 허용되지 않은 HTTP 메소드
            - 409 Conflict: 리소스의 현재 상태와 충돌
            - 429 Too Many Requests: 일정 시간 동안 너무 많은 요청
          - 5xx (서버 오류)
            - 500 Internal Server Error: 서버 내부 오류
            - 502 Bad Gateway: 게이트웨이나 프록시 서버가 upstream 서버로부터 유효하지 않은 응답을 받음
            - 503 Service Unavailable: 서버가 일시적으로 요청을 처리할 수 없음
            - 504 Gateway Timeout: 게이트웨이나 프록시 서버가 upstream 서버로부터 시간 내에 응답을 받지 못함
      - 연결 방식
        - HTTP/1.x에서는 기본적으로 연결 당 하나의 요청/응답만을 처리합니다. 그러나 다음과 같은 최적화 기법들이 있습니다.
          - Keep-Alive: 연결을 재사용하여 여러 요청/응답을 처리
            - 여러 HTTP 요청과 응답을 같은 연결로 처리
            - 일정 시간 또는 요청 수 후 연결 종료
            - 1.1에서는 기본적으로 활성화
            - 서버 리소스 관리를 위해 최대 연결 수 제한이 필요하며 HOL Blocking 문제가 발생할 수 있다.
          - Pipelining: 응답을 기다리지 않고 여로 요청을 연속해서 보냄
            - 여러 요청을 연속해서 보내고, 응답을 순차적으로 받음
            - Keep-Alive 연결 위에서 동작
            - 모든 브라우저/서버에서 지원되지 않음
            - FIFO 방식으로 처리하지만 복잡한 구현으로 인해 실제로는 잘 사용되지 않음
        - HTTP/2에서는 멀티플렉싱을 도입하여 하나의 연결로 여러 요청/응답을 동시에 처리할 수 있게 되었습니다.
          - Multiplexing
            - 하나의 TCP 연결에서 여러 스트림을 동시에 처리
            - 각 스트림은 독립적으로 요청/응답 처리
            - 바이너리 프로토콜 사용
            - 헤더 압축 (HPACK)
            - 서버 푸시 가능
            - 스트림 우선순위 지정 가능, 흐름 제어 메커니즘
            - HOL Blocking 문제 해결, 네트워크 리소스 효율적 사용
        - HTTP/3
          - QUIC 프로토콜
            - UDP 기반 QUIC 프로토콜 사용
            - 연결 설정 시 암호화와 전송 계층 동시 처리
            - 0-RTT 연결 설정 하능
            - 연결 마이그레이션 지원
            - 패킷 손실 감지와 복구 개선, 독립적 스트림 처리로 HOL 블로킹 완전 해결
            - 모바일 환경에서 특히 효과적이며 연결 설정 시간 대폭 단축
        > HTTP 연결 방식은 HTTP/1.0의 단기 연결부터 HTTP/3의 QUIC 프로토콜까지 크게 발전해왔습니다.<br> 
          HTTP/1.0에서는 매 요청마다 새로운 TCP 연결을 생성하고 종료하여 오버헤드가 컸습니다. HTTP/1.1에서는 
          Keep-Alive를 도입하여 하나으 ㅣ연결에서 여러 요청을 처리할 수 있게 되었고, 이는 성능 향상을 가져와습니다. 그러나 HOL Blocking 문제는 여전했습니다.<br>
          HTTP/2에서는 Multiplexing을 도입하여 하나의 연결에서 여러 스트림을 동시에 처리할 수 있게 되었습니다. 이는 HOL Blocking 문제를 크게 개선했고, 헤더 압축등의 기능으로 성능을 더욱 향상시켰습니다.<br>
          최신 버전인 HTTP/3는 UDP기반의 QUIC 프로토콜을 사용합니다. 이는 연결 설정 시간을 대폭 단축하고, 패킷 손실에 더 효과적으로 대응할 수 있게 해줍니다. 특히 모바일 환경에서 큰 이점을 제공하빈다.<br>
          연결 관리에 있어서 보안, 성능 최적화, 서버 구성, 클라이언트 구현 등 다양한 측면을 고려해야 합니다. 예를 들어 HTTPS 사용 시 TLS 세션 재사용을 통해 연결 설정 시간을 줄일 수 있고, Connection Pooling을 통해 연결을 효율적으로 관리할 수 있습니다.
      - 데이터 인코딩
        - URL 인코딩: 특수 문자를 안전하게 전송
          - 예약된 문자나 비ASCII 문자를 '%' 다음에 16진수 코드로 변환
          - UTF-8 인코딩 후 각 바이트를 퍼센트 인코딩
          - 브라우저와 서버에서 자동으로 처리되는 경우가 많음
        - BASE64 인코딩: 바이너리 데이터를 텍스틀 변환
          - 3바이트의 데이터를 4개의 6비트 그룹으로 변환
          - 각 그룹을 64개의 안전한 출력 문자로 매핑
          - 패딩: '=' 문자로 3의 배수가 아닌 입력 처리
          - 33% 정도 데이터 크기 증가
        - Content-Encoding
          - HTTP 메시지 본문의 압축을 통한 전송 효율 증대
            - gzip: 가장 널리 사용, DEFLATE 알고리즘 기반
            - deflate: DEFLATE 알고리즘 직접 사용
            - br: Brotli 압축, 보통 gzip보다 효율적
          - 클라이언트가 지원하는 인코딩을 Accept-Encoding 헤더로 전송
          - 서버가 적절한 인코딩 선택 후 Content-Encoding 헤더로 응답
          - CPU 사용과 압축률의 트레이드오프, 작은 페이로드의 경우 압축이 비효율적일 수 있음
        - Chunked transfer encoding: 큰 데이터를 작은 청크로 나눠 전송
          - 메시지 본문의 안전하고 효율적인 전송
          - 각 청크는 크기(16진수)와 데이터로 구성
          - 마지막 청크는 크기가 0
          - 청크 이후 선택적으로 트레일러 헤더 추가 가능
          - 동적 콘텐츠 생성 시 유용, 전체 크기를 모르는 상태에서 전송 시작 가능
      - 캐싱
        - HTTP는 캐싱 메커니즘을 제공하여 동일한 리소스에 대한 반복적인 요청을 최적화합니다.
        - 목적
          - 네트워크 트래픽 감소
          - 서버 부하 감소
          - 웹 페이지 로딩 속도 향상
        - 주요 캐시 위치
          - 브라우저 캐시
          - 프록시 캐시
          - 서버 측 캐시 (CDN 등)
        - 캐시 제어 헤더
          - Cache-Control
            - no-store: 캐시 저장 금지
            - no-cache: 재검증 없이 캐시 사용 금지
            - private: 브라우저만 캐시 가능
            - public: 공유 캐시(프록시 등)도 저장 가능
            - max-age: 캐시 유효 기간(초)
            - 예: Cache-Control: public, max-age=3600
          - Expires 헤더
            - 캐시 만료 날짜 지정
            - Cache-Control: max-age 가 있으면 무시됨
          - ETag 헤더
            - 리소스의 특정 버전에 대한 식별자
            - 변경 여부 확인에 사용
          - Last-Modified 헤더
            - 리소스의 마지막 수정 시간
            - ETag의 대안으로 사용
        - 조건부 요청
          - If-None-Match 헤더
            - 클라이언트가 가진 ETag 값 전송
            - 서버의 ETag와 일치하면 304 Not Modified 응답
          - If-Modified-Since 헤더
            - 클라이언트가 가진 Last-Modified 값 전송
            - 변경이 없으면 304 Not Modified 응답
        - 캐시의 동작
          - 첫 요청시
            - 클라이언트가 리소스 요청
            - 서버가 리소스와 캐시 헤더 응답
            - 클라이언트가 응답을 캐시에 저장
          - 후속 요청 시
            - 클라이언트가 캐시 확인
            - 유효하면 캐시된 리소스 사용
            - 만료되었으면 조건부 요청 발송
            - 서버가 304 또는 새 리소스 응답
            - 캐시 무효화 전략
        - CDN (Content Delivery Network)
          - 지리적으로 분산된 서버에 콘텐츠 캐시
          - 사용자와 가까운 서버에서 콘텐츠 제공
          - 로딩 속도 향상, 원본 서버 부하 감소, DDoS 공격 방어에 도움
        - 캐시 관련 보안
          - 민감한 정보
            - private 캐시 사용
            - 필요시 no-store 설정
          - API응답
            - 적절한 Cache-Control 설정
            - ETag 사용으로 효율적인 갱신
      - 쿠키
        - HTTP는 stateless 프로토콜이지만, 쿠키를 사용하여 클라이언트의 상태를 유지할 수 있습니다.
      > HTTP는 클라이언트-서버 모델을 기반으로 한 요청-응답 구졸르 사용하여 데이터를 전송합니다. 클라이언트가 
        특정 메소드를 사용하여 요청을 보내면, 서버는 해당 요청을 처리하고 상태 코드와 함께 응답을 반환합니다.<br>
        HTTP 메시지는 헤더와 본문으로 구성되며, 헤더에는 메타데이터가, 본문에는 실제 데이터가 포함됩니다.
        데이터는 다양한 방식(URL 인코딩, Base64 등)으로 인코딩될 수 있으며, 큰 데이터의 경우 chunked transfer encoding을 사용할 수 있습니다.<br>
        HTTP/1.x에서는 기본적으로 연결 당 하나의 요청/응답만 처리하지만, Keep-Alive나 Pipelining같은 최적화 기법을 사용할 수 있습니다. HTTP/2에서는 멀티플렉싱을 도입하여 성능을 크게 개선했습니다.<br>
        또한 HTTP는 캐싱 메커니즘을 제공하여 효율성을 높이고, 쿠키를 통해 상태 유지를 가능하게 합니다. 이러한 특성들로 인해 HTTP는 웹에서 가낭 널리 사용되는 프로토콜이 됐습니다.
    - HTTPS의 데이터 전송 방법
      - TLS 핸드셰이크 과정
        - 클라이언트 헬로
          - 클라이언트가 지원하는 암호화 알고리즘과 최신 TLS 버전 전송
        - 서버 헬로
          - 서버가 선택한 암호화 알고리즘과 TLS 버전 응답
          - 서버의 디지털 인증서 전송
        - 인증서 검증
          - 클라이언트가 서버의 인증서를 신뢰할 수 있는지 확인
        - 키 교환
          - 클라이언트가 세션 키 생성에 사용할 임시 키 생성
          - 서버의 공개 키로 암호화하여 전송
        - 세션 키 생성
          - 양측에서 동일한 세션 키 생성
        - 암호화 통신 시작
          - 생성된 세션 키로 대칭 암호화 통신 시작
      - 암호화 방식
        - 대칭키 암호화
          - 세션 키를 사용한 빠른 암호화/복호화 (예: AES)
        - 비대칭키 암호화
          - 키 교환 과정에서 사용 (예: RSA, ECC)
- 인증
  - HTTP: 서버의 신원을 확인할 수 없습니다.
  - HTTPS: SSL/TLS 인증서를 통해 서버의 신원을 확인할 수 있습니다.
- 속도
  - HTTP: 암호화 과정이 없어 상대적으로 빠릅니다.
  - HTTPS: 암호화/복호화 과정으로 인해 HTTP보다 약간 느릴 수 있지만 최신 기술로 그 차이가 미미해지고 있습니다.
- SEO (검색 엔진 최적화)
  - HTTP: 최근 검색 엔진들은 HTTPS 웹사이트를 선호합니다.
  - HTTPS: 검색 엔진 랭킹에 긍정적인 영향을 미칩니다.

## 답변 예시
HTTP와 HTTP의 주요 차이점은 보안성에 있습니다. HTTP는 데이터를 평문으로 전송하여 중간자 공격에 취약한 반면, HTTPS는 SSL/TLS를 
사용하여 데이터를 암호화합니다. 이로 인해 HTTPS는 데이터의 기밀성과 무결성을 보장하며, 서버의 신원도 확인할 수 있습니다.

또한, HTTPS는 검색 엔진 최적화(SEO)에도 긍정적인 영향을 미치며, 사용자에게 더 높은 신뢰를 제공합니다. 
비록 HTTPS가 암호화 과정으로 인해 약간의 성능 저하가 있을 수 있지만, 최신 기술의 발전으로 그 차이는 미미해지고 있스빈다.

따라서 현대 웹 개발에서는, 특히 민감한 정보를 다루는 웹사이트에서는 HTTPS의 사용이 필수적이라고 할 수 있습니다. 보안과 사용자 신뢰는 웹 서비스의 핵심 요소이기 때문입니다.