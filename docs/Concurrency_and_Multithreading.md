# 수정된 동시성 및 멀티스레딩 커리큘럼

## 1단계: CS 기초 이론
1. 운영체제 기초
    - [프로세스와 스레드의 개념](/240810/Process_and_Thread.md)
    - [컨텍스트 스위칭](/240810/Context_Switching.md)
    - [스케줄링 알고리즘](/240810/Scheduling.md)
    - 실습: 간단한 프로세스 관리자 구현

2. 메모리 관리
    - 가상 메모리
    - 페이징과 세그먼테이션
    - 캐시 일관성 문제
    - 실습: 메모리 할당 시뮬레이터 만들기

3. 동시성 기초
    - 동시성 vs 병렬성
    - 임계 영역과 레이스 컨디션
    - 데드락, 라이브락, 기아 현상
    - 실습: 간단한 동시성 문제 시뮬레이션

## 2단계: Java 스레드 기본
1. 스레드 생명주기
    - 스레드 상태 (NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED)
    - start(), run(), sleep(), join() 메소드의 이해
    - 실습: 다양한 상태의 스레드 생성 및 관찰

2. 스레드 동기화 기본
    - synchronized 키워드
    - volatile 키워드
    - wait(), notify(), notifyAll() 메소드
    - 실습: 동기화를 이용한 간단한 생산자-소비자 문제 구현

3. Java Memory Model (JMM)
    - happens-before 관계
    - 메모리 가시성 문제
    - 원자성과 가시성 보장 방법
    - 실습: JMM 관련 코드 예제 분석

## 3단계: 동시성 프로그래밍의 문제점과 해결 방법
1. 동시성 문제 심화
    - 데드락의 발생 조건과 예방 기법
    - 라이브락 시나리오와 해결 방법
    - 기아 현상 방지 전략
    - 실습: 동시성 문제 시나리오 구현 및 해결

2. 동시성 디버깅 기법
    - 스레드 덤프 분석
    - 경쟁 상태 탐지
    - 실습: 동시성 버그가 있는 프로그램 디버깅

## 4단계: 고급 동기화 기법
1. Lock 인터페이스와 구현체
    - ReentrantLock
    - ReadWriteLock
    - StampedLock
    - 실습: 다양한 Lock 구현체를 사용한 동기화 예제

2. Atomic 클래스들
    - AtomicInteger, AtomicLong, AtomicReference 등
    - Compare-and-Swap (CAS) 연산의 이해
    - 실습: Atomic 클래스를 이용한 스레드 안전 카운터 구현

3. 동기화 컬렉션
    - ConcurrentHashMap
    - CopyOnWriteArrayList
    - BlockingQueue 구현체들
    - 실습: 동기화 컬렉션을 활용한 멀티스레드 애플리케이션 구현

## 5단계: 동시성 디자인 패턴
1. 생산자-소비자 패턴
2. 읽기-쓰기 락 패턴
3. 불변 객체 패턴
4. 스레드 로컬 스토리지 패턴
- 실습: 각 패턴을 적용한 실제 문제 해결

## 6단계: Java 병렬 처리 프레임워크
1. Executor 프레임워크
    - ThreadPoolExecutor의 동작 원리
    - 다양한 종류의 스레드 풀 (Fixed, Cached, Scheduled)
    - 실습: Executor를 이용한 작업 처리 시스템 구현

2. Fork/Join 프레임워크
    - 작업 훔치기(work-stealing) 알고리즘
    - RecursiveTask와 RecursiveAction
    - 실습: Fork/Join을 이용한 병렬 정렬 알고리즘 구현

3. 병렬 스트림
    - 병렬 스트림의 내부 동작 원리
    - 효과적인 사용 방법과 주의사항
    - 실습: 병렬 스트림을 이용한 대용량 데이터 처리

## 7단계: 성능과 최적화
1. 성능 측정과 프로파일링
    - JMH (Java Microbenchmark Harness) 사용법
    - VisualVM, JConsole 등 모니터링 도구 활용
    - 실습: 멀티스레드 애플리케이션 성능 측정 및 분석

2. 동시성 버그 디버깅
    - 데드락 탐지와 해결
    - 레이스 컨디션 식별 방법
    - 실습: 실제 동시성 버그 찾기 및 수정

3. 확장성 있는 설계
    - 아마달의 법칙 이해와 적용
    - 동시성 제어의 세밀도(granularity) 조절
    - 실습: 확장성 있는 멀티스레드 애플리케이션 설계

## 8단계: 비동기 프로그래밍
1. CompletableFuture 심화
    - CompletableFuture의 다양한 메서드와 활용
    - 비동기 작업의 조합과 예외 처리
    - 실습: CompletableFuture를 이용한 복잡한 비동기 워크플로우 구현

2. 리액티브 프로그래밍 기초
    - 리액티브 프로그래밍의 개념과 이점
    - Reactor, RxJava 등 리액티브 라이브러리 소개
    - 실습: 간단한 리액티브 애플리케이션 구현

3. Project Reactor 심화
    - Flux와 Mono의 이해
    - 백프레셔(backpressure) 관리
    - 실습: Reactor를 이용한 비동기 데이터 처리 파이프라인 구현

## 9단계: 최신 동시성 기술
1. Java 9+ 동시성 기능
    - Flow API (Reactive Streams)
    - SubmissionPublisher와 Processor
    - 실습: Flow API를 이용한 발행-구독 시스템 구현

2. Project Loom (가상 스레드)
    - 가상 스레드의 개념과 이점
    - 기존 스레드 모델과의 차이점
    - 실습: 가상 스레드를 이용한 고성능 서버 애플리케이션 구현

3. 최신 동시성 라이브러리 탐구
    - Akka 프레임워크 소개
    - Kotlin 코루틴 기초
    - 실습: 선택한 최신 라이브러리를 이용한 동시성 프로그래밍