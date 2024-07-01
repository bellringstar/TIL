# WAS: 정의, 필요성, 대표 제품
## WAS의 정의
Web Application Server (WAS)는 웹 애플리케이션과 서버 환경을 만들어 동작시키는 기능을 제공하는 소프트웨어 프레임워크입니다.
### 주요 특징
1. 동적 콘텐츠 생성: 사용자 요청에 따라 실시간으로 콘텐츠를 생성
2. 비즈니스 로직 처리: 복잡한 애플리케이션 로직을 실행
3. 데이터베이스 연결: 영구 저장소와 상호작용 관리
4. 트랜잭션 관리: ACID 속성을 보장하는 트랜잭션 처리
5. 보안: 인증, 권한 부여 드으이 보안 기능 제공
### 아키텍처
```markdown
[클라이언트] <--> [웹 서버] <--> [WAS]
                            |
                            |--> [서블릿 컨테이너]
                            |--> [EJB 컨테이너 (Java EE의 경우)]
                            |--> [데이터베이스 연결 풀]
```
## WAS의 필요성
### 기술적 필요성
1. 동적 콘텐츠 정리: 정적 콘텐츠만으로는 복잡한 비즈니스 요구사항을 충족시킬 수 없습니다.
```java
@WebServlet("/dynamic")
public class DynamicContentServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String username = request.getParameter("username");
        response.setContentType("text/html");
        response.getWriter().println("<h1> Welcome, " + useranme + "!</h1>");
    }
}
```
2. 서버 사이드 프로그래밍: 클라이언트 측에서 처리하기 어려운 복잡한 연산과 데이터 처리를 서버에서 수행합니다.
3. 데이터베이스 연동: 영구적인 데이터 저장과 검색을 위해 데이터베이스와의 효율적인 연동이 필요합니다.
4. 세션 관리: 상태를 유지해야 하는 웹 애플리케이션을 위해 세션 관리 기능이 필요합니다.
### 비즈니스적 필요성
1. 확장성: 비즈니스 성장에 따라 시스템을 쉽게 확장할 수 있어야 합니다.
2. 유지보수성: 비즈니스 로직을 중앙화하여 유지 보수를 용이하게 합니다.
3. 보안: 중요한 비즈니스 로직과 데이터를 서버 측에서 안전하게 처리할 수 있습니다.
4. 성능: 서버 측 최적화를 통해 전체 시스템의 성능을 향상시킬 수 있습니다.
## 대표적인 WAS 소프트웨어
### Apache Tomcat
- 경량, 오픈소스, 서블릿 컨테이너
### JBoss/WildFly
- 완전한 Java EE 스택, 고성능, 클러스터링 지원
### IBM WebSphere
- 엔터프라이즈급, 고가용성, 강력한 관리도구
### Oracle WebLogic
- 대규모 트랜잭션 처리, 강력한 관리 기능, 클라우드 지워
## WAS 선택 기준 및 최적화
### 선택 기준
1. 성능 요구 사항
2. 확장성 및 클러스터링 지원
3. 관리 도구의 편의성
4. 라이선스 비용
5. 기술 지원 및 커뮤니티 활성도
### 성능 최적화
1. JVM 튜닝
2. 연결 풀링
3. 캐싱