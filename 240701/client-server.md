# 클라이언트-서버 모델
클라이언트-서버 모델은 분산 컴퓨팅의 기본 패러다임으로, 작업이나 워크로드를 요청하는 주체(클라이언트)와 이를 처리하고 응답하는 주체(서버)로 구성됩니다.
## 기술적 원리
1. 통신 프로토콜<br>
클라이언트와 서버 간 통신은 주로 TCP/IP 프로토콜 스택을 기반으로 합니다. 응용 계층에서는 HTTP, FTP, SMTP등 다양한 프로토콜이 사용됩니다.
```markdown
응용 계층 (HTTP, FTP, SMTP...)
          ↕
   전송 계층 (TCP, UDP)
          ↕
   인터넷 계층 (IP)
          ↕
   네트워크 인터페이스 계층
```
2. 요청-응답 모델<br>
- 클라이언트가 서버에 요청(request) 전송
- 서버가 요청을 처리
- 서버가 클라이언트에 응답(response) 전송
3. 구현 방식<br>
- 2티어 아키텍처
  - 클라이언트와 서버가 직접 통신하는 가장 기본적인 형태
- 3티어 아키텍처
  - 프레젠테이션 계층, 애플리케이션 계층, 데이터 계층으로 나뉩니다.
- N티어 아키텍처
  - 복잡한 시스템에서는 여러 계층으로 나눌 수 있습니다.
```markdown
[클라이언트] ←→ [로드 밸런서] ←→ [웹 서버] ←→ [애플리케이션 서버] ←→ [캐시 서버] ←→ [데이터베이스 서버]
```
4. 장단점
- 장점
  - 리소스의 중앙 관리 용이
  - 보안성 향상 (서버 측에서 데이터 보호 가능)
  - 확장성 (서버 능력 향상으로 전체 시스템 성능 개선 가능)
- 단점
  - 단일 실패 지점 (서버 장애 시 전체 시스템 마비)
  - 네트워크 의존성 (네트워크 문제 시 서브시 불가)
  - 비용 (고성능 서버 유지 비용)
5. 성능 고려사항
   - 네트워크 지연
     - 클라이언트와 서버 간 물리적 거리에 따른 지연을 최소화하기 위해 CDN을 사용할 수 있습니다.
   - 서버 부하 관리
     - 로드 밸런싱을 통해 여러 서버에 요청을 분산시킬 수 있스빈다.
```java
public class LoadBalancer {
    private List<Server> servers = new ArrayList<>();
    private int currentIndex = 0;
    
    public void addServer(Server server) {
        servers.add(server);
    }
    
    public Server getNextServer() {
        if (servers.isEmpty()) {
            return null;
        }
        Server server = servers.get(currentIndex);
        currentIndex = (currentIndex + 1) % servers.size();
        return server;
    }
}
```
   - 캐싱
     - 자주 요청되는 데이터를 캐시하여 응답 시간을 줄일 수 있습니다.
```java
public class CacheServer {
    private Map<String, Object> cache = new ConcurrentHashMap<>();
    
    public Object get(String key) {
        return cache.computeIfAbsent(key, this::loadFromDatabase);
    }
    
    private Object loadFromDatabase(String key) {
        // 데이터베이스에서 데이터 로드
    }
}
```
6. 현대적 변형 및 응용
- 마이크로서비스 아키텍처
  - 서비스를 작은 독립적인 단위로 분할하여 확장성과 유연성을 높입니다.
- 서버리스 컴퓨팅
  - AWS Lambda, Azure Functions 등을 통해 서버 관리 없이 코드 실행이 가능합니다.
- 엣지 컴퓨팅
  - 데이터 처리를 네트워크의 '엣지'로 이동시켜 지연시간을 줄입니다.
7. 실무 적용 사례
- 대규모 웹 서비스 아키텍처
```markdown
[사용자] → [CDN] → [로드 밸런서] → [웹 서버] → [애플리케이션 서버] → [캐시 서버] → [데이터베이스]
                                   ↑                ↑
                                   └── [모니터링 서버] ←┘
```
