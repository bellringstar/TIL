# Socket
ServerSocket이 연결을 준비한다면 여기서 accept()메서드로 연결이 완료됐을 때 반환하는 객체로 실제 통신 채널을 나타내는 중요한클래스이다.
### 1. 기본개념
- TCP/IP 프로토콜을 사용하는 두 호스트 간의 양방향 통신 링크를 나타냅니다.
- 입력 스트림과 출력 스트림을 가지고 있어 데이터를 주고받을 수 있습니다.
### 2. 주요 메서드와 속성
- getInputStream(): 소켓에서 데이터를 읽기 위한 InputStream을 반환합니다.
- getOutputStream(): 소켓으로 데이터를 쓰기 위한 OutputStream을 반환합니다.
- getInetAddress(): 연결된 원격 호스트의 주소를 반환합니다.
- getPort(): 연결된 원격 포트 번호를 반환합니다.
- getLocalPort(): 소켓이 바인딩된 로컬 포트 번호를 반환합니다.
- close(): 소켓 연결을 닫습니다.
### 3. 소켓 연결 설정
- setSoTimeout(int timeout): 읽기 작업의 타임아웃을 설정합니다.
- setTcpNoDelay(boolean on): TCP_NODELAY 옵션을 설정합니다.(Nagle 알고리즘 제어) -> 지연시간 감소 but 네트워크 오버헤드
- setKeepAlive(boolean on): SO_KEEPALIVE 옵션을 설정합니다. -> HTTP keepalive와 다른 네트워크 수준에서 동작. 비활성 연결이 여전히 유효한지 확인하는 것
```java
SeverSocket serverSocket = new ServerSocket(8080);
Socket clientSocket = serverSocket.accetp();

InputStream in = clientSocket.getInputStream();
OutputStream out = clientSocket.getOutputStream();

byte[] buffer = new byte[1024];
int bytesRead = in.read(buffer);

out.write("Hello, client!".getBytes());
clientSocket.close();
```
### 4. 블로킹 vs 논블로킹
- 기본적으로 Socket의 I/O 작업은 블로킹입니다.
- 논블로킹 모드를 위해서는 java.nio 패키지의 SocketChannel을 사용해야합니다.
### 5. 보안 고려사항
- SSL/TLS를 사용한 암호화 통신을 위해서는 javax.net.ssl.SSLSocket을 사용합니다.
### 6. 성능 관련 팁
- 버퍼 크기 최적화: 대용량 데이터 전송 시 버퍼 크기를 적절히 조정합니다.
- keepAlive 설정: 장기 연결에 유용하지만, 리소스 사용에 주의해야 합니다.
### 7. 예외 처리
- IOException: 네트워크 오류나 I/O작업 실패 시 발생
- SocketTimeoutException: 설정된 타임아웃 내에 작업이 완료되지 않을 때 발생합니다.
### 8. 멀티스레딩 고려사항
- Socket은 스레드 안전하지 않습니다. 여러 스레드에서 동시에 사용할 때 주의가 필요합니다.

## 소켓의 동작 원리
### 1. 네트워크 스택에서의 위치
- 소켓은 응용 계층과 전송 계층 사이의 추상화 계층입니다.
- TCP/IP 스택에서 운영체제 커널과 사용자 공간 애플리케이션 사이의 인터페이스 역할을 합니다.
### 2. 소켓의 내부 구조
- 파일 디스크립터: 운영체제 수준에서 소켓은 파일 디스크립터로 표현됩니다.
- 프로토콜 제어 블록(PCB): TCP 연결 상태, 시퀀스 번호, 윈도우 크기 등의 정보를 저장합니다.
- 송수신 버퍼: 커널 공간에 위치하며, 데이터의 임시 저장소 역할을 합니다.
### 3. 소켓 생성 과정
- socket() 시스템 콜: 커널에 소켓 생성을 요청합니다.
- 커널은 파일 디스크립터 테이블에 새 항목을 추가하고, 관련 자료 구조를 초기화합니다.
### 4. 연결 설정 메커니즘
- TCP의 경우 3-way handshake 과정이 소켓 레벨에서 추상화됩니다.
- connect() 호출 시, 커널은 SYN 패킷을 생성하고 전송합니다.
- accept()는 완전히 설정된 연결(ESTABLEHED 상태)만을 반환합니다.
### 5. 데이터 전송 원리
- write()/send() 호출: 데이터는 먼저 소켓의 송신 버퍼에 복사됩니다.
- 커널은 이 데이터를 TCP 세그먼트로 분할하고, IP 패킷으로 캡슐화하여 전송합니다.
- read()/recv() 호출: 수신 버퍼에서 데이터를 사용자 공간으로 복사합니다.
### 6. 블로킹 vs 논블로킹
- 블로킹 모드: 기본 설정으로 I/O 작업이 완료될 때까지 스레드를 블록합니다.
- 논블로킹 모드: O_NONBLOCK 플래그 설정으로 즉시 반환되며, 완료 여부를 반환 값으로 확인합니다.
### 7. I/O 멀티플렉싱
- select(), poll(), epoll() 등의 시스템 콜을 통해 여러 소켓을 효율적으로 관리합니다.
- 이벤트 기반 처리로 높은 동시성을 달성할 수 있습니다.
### 소켓 옵션과 최적화
- TCP_NODELAY (Nagle 알고리즘 비활성화): 작은 패킷의 즉시 전송을 가능케 합니다.
- SO_RCVBUF/SO_SNDBUF: 버퍼 크기 조정으로 처리량을 최적화합니다.
- TCP_QUICKACK: 지연된 ACK를 비활성화하여 응답성을 높입니다.
### 소켓과 메모리 관리
- Zero-copy 기술: sendfile() 시스템 콜을 사용하여 커널-유저 공간 간 불필요한 복사를 줄입니다.
- 메모리 맵 I/O: mmap()을 통해 파일을 직접 메모리에 매핑하여 I/O성능을 향상시킵니다.
### 보안 측면
- 소켓 필터링: 커널 레벨에서 패킷 필터링을 통해 보안을 강화합니다.
- TLS/SSL: 전송 계층에서의 암호화를 통해 데이터 보안을 확보합니다.
### 소켓의 상태 기계
- TCP 소켓은 내부적으로 복잡한 상태 기계를 가지고 있으며, 이는 RFC 794에 정의되어 있습니다.
- CLOSED, LISTEN, SYN_SENT, SYN_RECEIVED, ESTABLISHED 등의 상태를 거칩니다.
### 성능 고려사항
- 컨텍스트 스위칭: 잦은 시스템 콜은 컨텍스트 스위칭 오버헤드를 증가시킵니다.
- 인터럽트 결합: 네트워크 카드 레벨에서 여러 패킷을 그룹화하여 처리합니다.
### 소켓과 프로세스 관리
- fork()후 소켓 상속: 자식 프로세스는 부모의 소켓을 상속받아 사용할 수 있습니다.
- SO_REUSEADDR: 프로세스 재시작 시 동일한 주소/포트 바인딩을 가능하게 합니다.