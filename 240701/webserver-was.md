# 웹 서버 vs 애플리케이션 서버
## 정의 및 기본 개념
### 웹 서버
웹 서버는 주로 정적 콘텐츠를 처리하고 HTTP 프로토콜을 통해 클라이언트 요청에 응답하는 소프트웨어입니다.<br>
예) Apache HTTP Server, Nginx
### 애플리케이션 서버
애플리케이션 서버는 동적 콘텐츠를 생성하고, 비즈니스 로직을 실행하며, 데이터베이스와의 상호작용을 관리합니다.<br>
예)Tomcat, JBoss/WildFly, WebSphere
## 아키텍처 및 동작 원리
### 웹 서버 아키텍처
웹 서버는 일반적으로 다음과 같은 구조로 동작합니다.
1. 요청 수신
2. 요청 파싱
3. 정적 파일 검색
4. 응답 생성 및 전송
```java
public class SimpleWebServer {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(8080);
        while (true) {
            Socket clientSocket = serverSocket.accept();
            BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
            PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true);
            
            String request = in.readLine();
            // 요청 파싱 및 정적 파일 검색
            String response = "HTTP/1.1 200 OK\r\n\r\nHello, World!";
            
            clientSocket.close();
        }
    }
}
```
### 애플리케이션 서버 아키텍처
애플리케이션 서버는 더 복잡한 구조를 가지며, 일반적으로 다음과 같이 동작합니다.
1. 요청 수신
2. 요청 파싱
3. 서블릿/컨트롤러 매핑
4. 비즈니스 로직 실행
5. 데이터베이스 상호 작용
6. 동적 콘텐츠 생성
7. 응답 생성 및 전송
```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String name = request.getParameter("name");
        // 비즈니스 로직 실행
        String greeting = generateGreeting(name);
        // 동적 콘텐츠 생성
        response.setContentType("text/html");
        response.getWriter().println("<html><body><h1>" + greeting + "</h1></body></html>");
    }
    
    private String generateGreeting(String name) {
        return "Hello, " + name + "!";
    }
}
```
## 주요 차이점
### 기능적 차이
1. 정적 vs 동적 콘텐츠
- 웹 서버: 주로 정적 콘텐츠 처리 
- 애플리케이션 서버: 동적 콘텐츠 생성 및 처리
2. 비즈니스 로직
- 웹 서버: 제한적인 비즈니스 로직 처리(예: 리다이렉션, 간단한 인증)
- 애플리케이션 서버: 복잡한 비즈니스 로직 실행 가능
3. 데이터베이스 연결
- 웹 서버: 직접적인 데이터베이스 연결 기능 없음
- 애플리케이션 서버: 데이터베이스 연결 풀 관리, ORM 지원 등
### 성능 특성
1. 처치량
- 웹 서버: 정적 콘텐츠 처리에 최적화되어 높은 처리량
- 애플리케이션 서버: 동적 처리로 인해 상대적으로 낮은 처리량
2. 리소스 사용
- 웹 서버: 상대적으로 적은 리소스 사용
- 애플리케이션 서버: 더 많은 메모리와 CPU 리소스 필요
3. 확장성
- 웹 서버: 수평적 확장이 용이
- 애플리케이션 서버: 상태 관리로 인해 확장이 더 복잡할 수 있음
## 왜 구분이 필요한가
1. 관심사의 분리: 정적 콘텐츠 처리와 동적 콘텐츠 생성의 분리로 각 부분을 독립적으로 최적화할 수 있습니다.
2. 보안: 웹 서버를 프록시로 사용하여 애플리케이션 서버를 직접적인 외부 접근으로부터 보호할 수 있습니다.
3. 성능 최적화: 각 서버의 특성에 맞는 최적화 전략을 적용할 수 있습니다.
4. 유연한 아키텍처: 필요에 따라 웹 서버만 사용하거나, 애플리케이션 서버와 조합하여 사용할 수 있습니다.
## 실무 적용 및 최적화
### 웹 서버와 애플리케이션 서버의 조합
```markdown
[클라이언트] <--> [웹 서버(Nginx)] <--> [애플리케이션 서버(Tomcat)]

nginx 설정 예;
http {
upstream app_servers {
server app1.example.com:8080;
server app2.example.com:8080;
}

    server {
        listen 80;
        server_name example.com;

        location / {
            proxy_pass http://app_servers;
        }

        location /static/ {
            root /var/www/static;
        }
    }
}
```
### 성능 최적화 전략
1. 웹 서버 최적화
- 정적 파일 캐싱
- GZip 압축
- HTTP/2 지원
2. 애플리케이션 서버 최적화
- 연결 풀링
- JVM 튜닝
- 비즈니스 로직 최적화
### 로드 밸런싱
대규모 시스템에서는 로드 밸런서를 사용하여 여러 웹 서버와 애플리케이션 서버로 트래픽을 분산시킵니다.
```markdown
[클라이언트] <--> [로드 밸런서] <--> [웹 서버들] <--> [애플리케이션 서버들]
```