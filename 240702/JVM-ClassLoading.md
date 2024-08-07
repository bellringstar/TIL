# JVM의 클래스 로딩 메커니즘
## 1. 기본 동작 원리
### 1. 클래스 로딩 과정
1. 로딩
- 클래스 파일(.class)을 읽어 메모리에 할당합니다.
- 클래스 파일의 바이너리 데이터를 메모리로 가져옵니다.
- 이 단계에서 클래스 로더(ClassLoader)를 사용합니다.
2. 링킹
- 로드된 클래스 파일의 구조를 확인하고 이를 실행 가능한 상태로 만듭니다.
- 링킹 과정은 세 단계로 나눠집니다.
  - 검증
    - 클래스 파일 형식이 유요한지 검사합니다.
    - 자바 언어 명세(JLS)에 따른 형식 검사를 수행합니다.
  - 준비
    - 클래스의 정적 변수를 기본 값으로 초기화합니다.
    - 예를 들어, 정수형은 0으로 참조형은 null로 초기화됩니다.
  - 해결
    - 심볼릭 레퍼런스를 메모리 레퍼런스로 변경합니다.
    - 예를 들어, 메서드 호출이나 필드 접근에 사용되는 심볼릭 이름을 실제 메모리 주소로 변경합니다.
3. 초기화
    - 클래스 변수를 초기화하고, 정적 초기화 블록을 실행합니다.
    - 이 단계에서 프로그램 코드가 실행됩니다.
    - 클래스의 static 블록이나 static 필드의 초기화가 이 단계에서 수행됩니다.
4. 사용
    - 클래스의 인스턴스를 생성하거나 정적 메서드를 호출할 수 있습니다.
    - 이 시점부터 클래스는 완전히 초기화되어 사용 가능합니다.
## 2. 왜 이렇게 동작하는가?
### 1. 안정성과 일관성
- 클래스 로딩 과정은 프로그램의 안정성과 일관성을 보장하기 위해 설계되었습니다.
- 클래스 파일의 형식 검증과 심볼릭 레퍼런스의 해소는 프로그램이 잘못된 형식의 클래스를 로드하거나 잘못된 메서드/필드 참조로 인해 충돌하는 것을 방지합니다.
### 2. 지연 로딩
- 지연 로딩은 프로그램의 시작 시간과 메모리 사용을 최적화하기 위한 중요한 매커니즘입니다.
- 클래스는 실제로 필요할 때까지 로드되지 않으므로, 초기 로딩 시간과 메모리 사용을 줄일 수 있습니다.
- 이는 프로그램이 필요하지 않은 클래스를 미리 로드하지 않도록 함으로써 성능을 최적화합니다.
### 3. 동적 로딩
- JVM은 동적으로 클래스를 로드할 수 있습니다. 이를 통해 프로그램 실행 중에 새로운 클래스를 로드하거나 기존 클래스를 교체할 수 있습니다.
- 동적 로딩은 리플렉션과 같은 메커니즘을 사용하여 프로그램의 유연성과 확장성을 높입니다.
## 3. 기술적 디테일
### 1. 클래스 로더
클래스 로더는 클래스 파일을 찾아서 읽어들이는 역할을 합니다. 자바에서 클래스 로더는 계층 구조를 가집니다.
1. 부트스탭 클래스 로더
- JVM의 가장 기본 클래스 로더로, JDK의 기본 클래스(예: java.lang.* 패키지)를 로드합니다.
- 네이티브 코드로 구현되어 있으며, 파일 시스템에서 클래스 파일을 찾습니다.
2. 확장 클래스 로더
- 부트스트랩 클래스 로더의 자식으로, JDK 확장 디렉토리('lib/ext')의 클래스를 로드합니다.
3. 애플리케이션 클래스 로더
- 사용자 애플리케이션의 클래스를 로드합니다.
- 클래스패스(classpath)에 지정된 경로에서 클래스를 찾습니다.
### 2. 클래스 검증
1. 파일 형식 검증
- 클래스 파일이 올바른 매직 넘버로 시작하는지 확인합니다.
- 클래스 파일의 버전이 JVM과 호환되는지 확인합니다.
2. 바이트코드 검증
- 바이트코드가 유효한 명령어로 구성되어 있는지 확인합니다.
- 스택과 변수 사용이 일관성 있는지 확인합니다.
3. 참조 검증
- 클래스가 참조하는 모든 필드와 메서드가 실제로 존재하는지 확인합니다.
- 접근 제어자가 올바르게 설정되어 있는지 확인합니다.
### 3. 심볼릭 레퍼런스 해소
심볼릭 레퍼런스 해소는 클래스 파일 내의 심볼릭 이름을 실제 메모리 주소로 변환하는 과정입니다.
1. 필드 해소
- 필드의 이름과 타입을 찾아 실제 필드에 대한 메모리 참조로 변환합니다.
2. 메서드 해소
- 메서드의 이름과 시그니처를 찾아 실제 메서드에 대한 메모리 참조로 변환합니다.
3. 클래스 해소
- 클래스 이름을 찾아 실제 클래스 객체로 변환합니다.