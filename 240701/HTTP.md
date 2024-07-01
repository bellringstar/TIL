# HTTP
## HTTP 요청-응답 사이클
HTTP는 클라이언트-서버 모델을 기반으로 동작하며 다음과 같은 단계로 진행됩니다.
1. 클라이언트가 서버에 요청 전송
2. 서버가 요청을 처리
3. 서버가 클라이언트에 응답 전송
### TCP 연결 설정
HTTP/1.1 이상에서는 기본적으로 지속 연결을 사용합니다. 연결 과정은 다음과 같습니다.
1. TCP 3-way handshake
2. (선택적) TLS handshake(HTTPS의 경우)
```java
Socket socket = new Socket("example.com", 80);
OutputStream out = socket.getOutputStream();
InputStream in = socket.getInputStream();
```
### 요청 구조
HTTP 요청은 다음 구조를 가집니다.
1. 요청 라인: 메서드, URI, HTTP 버전
2. 헤더 필드
3. 빈줄
4. 메시지 본문(선택)
```markdown
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: text/html
```
```java
URL url = new URL("http://example.com");
HttpURLConnection conn = (HttpURLConnection) url.openConnection();
conn.setRequestMethod("GET");
conn.setRequestProperty("User-Agent", "Mozilla/5.0");

int responseCode = conn.getResponseCode();
```
### 응답 구조
HTTP 응답은 다음 구조를 가집니다.
1. 상태 라인: HTTP 버전, 상태 코드, 상태 메시지
2. 헤더 필드
3. 빈 줄
4. 메시지 본문
```markdown
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 1234

<!DOCTYPE html>
<html>...
```
```java
BufferedReader in = new BufferedReader(new InputStreamReader(conn.getInputStream()));
String inputLine;
StringBuilder response = new StringBuilder();
while ((inputLine = in.readLine()) != null) {
    response.append(inputLine);
}
in.close();
```
## HTTP 메서드의 동작
주요 HTTP 메서드의 동작 원리
### GET
리소스 조회에 사용됩니다. 멱등성(idempotent)을 가지며, 캐시 가능합니다.
```java
@GetMapping("/users/{id}")
public ResponseEntity<User> getUser(@PathVariable Long id) {
    User user = userService.findById(id);
    return ResponseEntity.ok(user);
}
```
### POST
리소스 생성에 사용됩니다. 비멱등적이며, 일반적으로 캐시되지 않습니다.
```java
@PostMapping("/users")
public ResponseEntity<User> createUser(@RequestBody User user) {
    User createdUser = userService.create(user);
    return ResponseEntity.status(HttpStatus.CREATED).body(createdUser);
}
```
### PUT
리소스 갱신에 사용됩니다. 멱등성을 가집니다.
```java
@PutMapping("/users/{id}")
public ResponseEntity<User> updateUser(@PathVariable Long id, @RequestBody User user) {
    User updatedUser = userService.update(id, user);
    return ResponseEntity.ok(updatedUser);
}
```
## 상태 관리 메커니즘
HTTP는 기본적으로 무상태(stateless) 프로토콜이지만, 다음과 같은 방법으로 상태를 관리합니다.
### 쿠키(Cookie)
서버가 클라이언트에 상태 정보를 저장하는 메커니즘입니다.
```java
@GetMapping("/login")
public ResponseEntity<String> login(HttpServletResponse response) {
    Cookie cookie = new Cookie("sessionId", generateSessionId());
    cookie.setMaxAge(3600);
    response.addCookie(cookie);
    return ResponseEntity.ok("Logged in");
}
```
### 세션(Session)
서버 측에서 클라이언트의 상태를 유지하는 메커니즘입니다.
```java

@GetMapping("/dashboard")
public ResponseEntity<String> dashboard(HttpSession session) {
    String userId = (String) session.getAttribute("userId");
    if (userId == null) {
        return ResponseEntity.status(HttpStatus.UNAUTHORIZED).body("Not logged in");
    }
    return ResponseEntity.ok("Welcome, user " + userId);
}
```
## 캐싱 메커니즘
HTTP는 성능 향상을 위해 다양한 캐싱 전략을 제공합니다.
### ETag
리소스의 특정 버전에 대한 식별자입니다.
```java
@GetMapping("/articles/{id}")
public ResponseEntity<Article> getArticle(@PathVariable Long id, @RequestHeader("If-None-Match") String ifNoneMatch) {
  Article article = articleService.findById(id);
  String etag = generateETag(article);

  if (etag.equals(ifNoneMatch)) {
    return ResponseEntity.status(HttpStatus.NOT_MODIFIED).build();
  }

  return ResponseEntity.ok().eTag(etag).body(article);
}
```
### Cache-Control
캐시 동작을 지정하는 헤더입니다.
```java
@GetMapping("/static-content")
public ResponseEntity<String> getStaticContent() {
    String content = "This content can be cached";
    return ResponseEntity.ok()
        .cacheControl(CacheControl.maxAge(1, TimeUnit.HOURS))
        .body(content);
}
```
## 네트워크 레벨에서의 HTTP 동작
실제 네트워크에서 HTTP가 어떻게 동작하는지 살펴보겠습니다.
1. DNS 조회: 도메인 이름을 IP 주소로 변환
2. TCP 연결 설정 (3-way handshake)
3. HTTP 요청 전송
4. 서버의 요청 처리
5. HTTP 응답 전송
6. 연결 종료 또는 유지(Keep-Alive)
```java
public class HTTPSimulation {
    public static void main(String[] args) {
        // 1. DNS 조회
        InetAddress address = InetAddress.getByName("www.example.com");
        
        // 2. TCP 연결 설정
        Socket socket = new Socket(address, 80);
        
        // 3. HTTP 요청 전송
        PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
        out.println("GET / HTTP/1.1");
        out.println("Host: www.example.com");
        out.println();
        
        // 4&5 서버의 응답 수신
        BufferedReader in = new BufferdReader(new InputStreamReader(socket.getInputStream()));
        String line;
        while ((line = in.readLine()) != null) {
          System.out.println(line);
        }
        
        // 6. 연결 종료
        socket.close();
    }
}
```
## 서버 측 처리 과정
서버에서 HTTP 요청을 처리하는 일반적인 단계는 다음과 같습니다.
1. 요청 파싱
2. 라우팅
3. 인증/인가 (필요시)
4. 비즈니스 로직 처리
5. 응답 생성
```java
@WebServlet("/example")
public class ExampleServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // 1. 요청 파싱 (서블릿 컨테이너가 수행)
        // 2. 라우팅 (서블릿 매핑에 의해 이미 수행됨)
        // 3. 인증/인가
        if (!isAuthorized(request)) {
            response.sendError(HttpServletResponse.SC_UNAUTHORIZED);
        }
        // 4. 비즈니스 로직 처리
        String result = processBusinessLogic(request);
        
        // 5. 응답 생성
        response.setContentType("text/plain");
        response.getWriter().write(result);
    }
}
```
