# Java Thread

## Thread의 기본 개념
Thread는 프로그램 내에서 동시에 실행될  수 있는 가장 작은 실행 단위입니다.
### 왜 Thread를 사용하는가?
- 병렬 처리
  - 멀티코어 프로세서를 효율적으로 활용하기 위함입니다.
- 응답성 향상
  - UI Thfead와 작업 Thread를 분리하여 사용자 경험을 개선합니다.
- 자원 공유
  - 여러 작업 간 효율적인 자우너 공유가 가능합니다.
### 동작 원리
1. Thread 생성 : 운영체제에 Thread 생성 요청
2. 스택 할당 : 각 Thread에 독립적인 스택 메모리 할당 (일반적으로 1MB, 애플리케이션 실행시 크기 설정 가능)
3. 레지스터 설정 : PC(Program Counter), SP(Stack Pointer) 등의 레지스터 초기화
4. Thread 스케쥴링 : 운영체제의 스케줄러에 의해 실행 시간과 순서 결정
<br>Thread는 JVM 내에서 독립적인 호출 스택을 가지고 실행됩니다.
> 용어 설명
> - 병렬 처리 : 여러 작업을 동시에 처리
> - 멀티코어 프로세서 : 하나의 CPU에 여러 개의 처리 코어가 있는 구조
> - 스택(Stack) : 지역변수, 매개변수, 반환 주소 등을 저장하는 메모리 영역
> - PC(Program Counter) : 다음에 실행할 명령어의 주소를 가리키느 레지스터
> - SP(Stack Pointer) : 현재 스택의 최상단 주소를 가리키는 레지스터

## JVM에서의 Thread 구현
JVM은 Native Thread Model을 사용합니다.
### 왜 Native Thread Model인가?
- 운영체제의 Thread 스케쥴링을 직접 활용할 수 있어 효율적입니다.
- 시스템 자원을 더 효과적으로 활용할 수 있습니다.
- 네이티브 코드와의 상호 운용성이 좋습니다.
### 어떻게 구현되는가?
- Java Thread는 운영체제의 네이티브 Thread와 1:1로 매핑됩니다.
- JNI(Java Native Interface)를 통해 운영체제의 Thread API를 호출합니다.
### 동작 원리
1. JNI 호출 : Java에서 Thread 생성 시 JNI를 통해 운영체제의 THRead 생성 API 호출
2. 운영체제 Thread 생성 : 예를 들어 Linux에서는 clone() 시스템 콜을 사용하여 새로운 Thread 생성
3. Thread 매핑 : 생성된 운영체제 Thread와 Java Thread 객체를 1:1로 매핑
4. 컨텍스트 전환 : Thread 간 전환 시 운영체제의 컨텍스트 스위칭 메커니즘 사용
> 용어 설명
> - Native Thread Model : Java Thread를 운영체제의 Thread와 1:1로 매핑하는 모델
> - JNI(Java Native Interface) ; Java코드와 네이티브 코드(C, C++등) 간의 상호 운용을 위한 프레임워크
> - 시스템 콜 : 운영체제의 커널이 제공하는 서비스를 호출하는 프로그래밍 인터페이스
> - 컨텍스트 전환 : CPU가 현재 실행 중인 Thread에서 다른 Thread로 제어를 넘기는 화정

## Thread의 생명 주기
Thread의 상태 변화를 이해하는 것이 중요합니다.
### 왜 생명 주기를 알아야 하는가?
- Thread의 현재 상태를 파악하여 효율적인 관리가 가능하빈다.
- Dead Lock, Starvation 등의 문제 해결에 필수적입니다.
- 성능 최적화와 디버깅에 중요한 정보를 제공합니다.
### Thread 의 상태
- New : Thread 객체가 생성된 상태
- Runnable : start() 메서드 호출 후, 실행 대기 상태
- Running : 실제로 CPU를 점유하여 실행 중인 상태
- Blocked : 동기화 블록 진입을 기다리는 상태
- Waiting : 다른 Thread의 특정 작업 완료를 기다리는 상태
- Timed Waiting : 지정된 시간 동안 대기하느 상태
- Terminated : run() 메서드 실행 완료 또는 예외 발생으로 종료된 상태
### 동작 원리
1. New : Thread 객체 생성 시 메모리 할당 및 초기화
2. Runnable : start() 호출 시 운영체제에 Thread 실행 요청
3. Running : 운영체제의 스케줄러에 의해 CPU 시간 할당 받아 실행
4. Blocked/Waiting : 
    - Blocked : 동기화 블록 진입 시 운영체제의 mutex나 세마포어 사용
    - Waiting : wait() 호출 시 Thread를 wait queue에 넣고 스케쥴링에서 제외
5. Terminated : run() 메서드 종료 시 스택 메모리 해제 및 운영체제에 Thread 종료 통보
> 용어 설명
> - Dead Lock : 두 개 이상의 Thread가 서로의 작업이 끝나기를 기다리며 영구적으로 멈춘 상태
> - Starvation : 특정 Thread가 계속해서 자원을 할당받지 못하는 상태
> - Mutex(Mutual Exclusion) : 공유 자원에 대한 접근을 동기화하는 기법
> - 세마포어(Semaphore) : 공유 자원에 대한 접근을 제어하는 데 사용되는 동기화 기법
> - Wait Queue : 특정 조건을 기다리는 Thread들이 대기하는 큐

## Thread 생성 방법
Thread 클래스 상속 vs Runnable 인터페이스 구현
### 왜 두 가지 방법이 존재하는가?
- 유연성 : Runnable은 다중 상속의 제약을 받지 않습니다.
- 재사용성 : Runnable은 동일한 객체를 여러 Thread에서 실행 가능합니다.
```java
public class MyThread extends Thread {
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        MyThread thread = new MyThread();
        thread.start();
    }
    
}

public class MyRunnable implements Runnable {
    @Override
    public void run() {
        for (int i = 0; i < 5; i++) {
            System.out.println(Thread.currentThread().getName() + ": " + i);
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    public static void main(String[] args) {
        Thread thread = new Thread(new MyRunnable());
        thread.start();
    }
}
```
### 동작 원리
1. 객체 생성 : Thread 객체 또는 Runnable 구현 객체 생성
2. 스택 할당 : JVM이 새 Thread를 위한 스택 메모리 할당
3. Thread 객체 초기화 : Thread 상태, 우선순위 등 설정
4. 네이티브 Thread 생성 : JNI를 통해 운영체제의 Thread 생성 API 호출
5. Thread 동기화
### 왜 동기화가 필요한가?
- Race Condition 방지 : 여러 Thread가 공유 자원에 동시 접근 시 발생하는 문제 해결
- 데이터 일관성 유지 : 한 Thread의 변경 사항을 다른 THREad에게 정확히 반영
```java
public class Counter {
    private int count = 0;
    
    public synchronized void increment() {
        count++;
    }
    
    public synchronized int getCount() {
        return count;
    }

    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                counter.increment();
            }
        });
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 10000; i++) {
                counter.increment();
            }
        });
        
        t1.start();
        t2.start();
        
        // main 스레드가 모든 스레드의 작업이 끝날 때 까지 기다게 한다.
        t1.join();
        t2.join();

        System.out.println("Final count : " + counter.getCount());
    }
}
```
```java
public class Counter {
    private int count = 0;
    private Lock lock = new ReentrantLock();
    
    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }
    
    public int getCount() {
        lock.lock();
        try {
            return count;
        } finally {
            lock.unlock();
        }
    }
}
```
### 동작 원리(Synchronized)
1. 모니터 진입: Thread가 synchronized 블록/메서드 진입 시 객체의 모니터 획득 시도
2. 락 획독 : 모니터 획득 성공 시 임계 영역 실행, 실패 시 모니터 진입 대기열에서 대기
3. 락 해제 : synchronized 블록/메서드 종료 시 모니터 해제
4. 대기 THREAD 깨우기 : 모니터 해제 후 대기 중인 Thread 중 하나를 꺠워 실행 기회 제공

### 동작 원리(Lock)
1. ReentrantLock : 더 정밀한 동기화 제어가 필요한 경우 사용할 수 있는 락
2. lock.lock() 메서드를 호출하여 락을 획득

> 용어 설명
> - Race Condition : 여러 Thread가 공유 데이터에 동시에 접근하여 예상치 못한 결과를 만드는 상황
> - 임계 영역 : 여러 Thread가 동시에 접근하면 안 되는 공유 자원을 접근하는 코드 영역
> - 모니터 : 객체에 대한 동기화된 접근을 제공하는 동기화 구조

## volatile 키워드
Java에서 멀티스레드 프로그래밍 시 변수의 값을 정확하게 읽고 쓰기 위해 사용되는 키워드입니다. 주로 변수의 최신 값을 모든 스레드에서 즉시 볼 수 있게 하여 데이터 일관성을 보장하는 데 사용됩니다.
### 왜 Volatile을 사용하는가?
- 가시성 보장 : 변수의 변경사항을 즉시 다른 Thread에 반영<br>
이는 해당 변수가 수정될 때마다 즉시 메인 메모리에 기록되고 읽힐 때마다 메인 메모리에서 읽힌다는 것을 의미.
- 원자성은 보장하지 않음 : 복잡한 연산에는 부적합

```java
public class SharedFlag {
    private volatile boolean flag = false;

    public void setFlag(boolean flag) {
        this.flag = flag;
    }

    public boolean isFlag() {
        return flag;
    }

    public static void main(String[] args) {
        SharedFlag sharedFlag = new SharedFlag();
        
        Thread writerThread = new Thread(() -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            sharedFlag.setFlag(true);
            System.out.println("Flag set to true");
        });
        
        Thread readerThread = new Thread(() -> {
            while (!sharedFlag.isFlag()) {
                // 기다림
            }
            System.out.println("Flag is now true");
        });
        
        writerThread.start();
        readerThread.start();
    }
}
```
### 동작 원리
1. 메모리 배리어 생성 : volatile 변수 접근 전후로 메모리 배리어 생성
2. 캐시 무효화 : 변수 수정 시 모든 CPU 캐시에서 해당 변수 무효화
3. 메인 메모리 동기화 : 변수 읽기/쓰기 시 항상 메인 메모리와 동기화
> 용어 설명
> - 가시성 : 한 Thread에서 변경한 변수의 값이 다른 Thread에 즉시 보이는 것
> - 원자성 : 작업이 중간에 중단되지 않고 완전히 수행되거나 전혀 수행되지 않는 성질
> - 메모리 배러어 : 메모리 연산의 순서를 제어하는 CPU 명령어

## Thread Pool (ExecutorService 사용)
### 왜 Thread Pool을 사용하는가
- Thread 생성/소멸 비용 감소
- 자원 관리의 효율성 증가
- 작업 큐잉과 실행의 분리
```java
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class ThreadPoolExample {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(5);

        for (int i = 0; i < 10; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("Task " + taskId + " is running on " + Thread.currentThread().getName());
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }

        executor.shutdown();
    }
}
```
### 동작 원리
1. Thread Pool 생성 : 지정된 수의 Thread를 미리 생성하여 대기 사앹로 유지
2. 작업 제출 : submit() 또는 execute() 메서드를 통해 작업(Runnable 또는 Callable)을 제출
3. 작업 큐잉 : 제출된 작업은 내부 작업 큐(BlockingQueue)에 저장
4. 작업 실행 : 유휴 Thread가 작업 큐에서 작업을 가져와 실행
5. 작업 완료 : 작업 완료 후 Thread는 다시 유휴 상태로 돌아가 다음 작업을 대기
### Thread Pool의 종류
1. Fixed Thread Pool : 고정된 수의 Thread
2. Cached Thread Pool : 필요에 따라 Thread를 생성하고 재사용
3. Single Thread Executor : 단일 Thread로 모든 작업을 순차적으로 처리
4. Scheduled Thread Pool : 주기적인 작업 실행에 사용
> 용어 설명
> - ExecutorService: Thread Pool을 관리하는 인터페이스
> - BlockingQueue: Thread-safe한 작업 대기열
> - Runnable: 실행할 작업을 정의하는 인터페이스
> - Callable: 결과를 반환하는 작업을 정의하는 인터페이스

## Fork/Join 프레임워크
### 왜 Fork/Join 프레임워크를 사용하는가?
- 대규모 작업을 작은 단위로 분할하여 병렬 처리
- 'work-stealing'알고리즘을 통한 부하 분산
- 멀티코어 프로세서의 효율적인 활용
```java
import java.util.concurrent.RecursiveTask;
import java.util.concurrent.ForkJoinPool;

public class FibonacciTask extends RecursiveTask<Integer> {
    final int n;

    FibonacciTask(int n) { this.n = n; }

    protected Integer compute() {
        if (n <= 1)
            return n;
        FibonacciTask f1 = new FibonacciTask(n - 1);
        f1.fork();
        FibonacciTask f2 = new FibonacciTask(n - 2);
        return f2.compute() + f1.join();
    }

    public static void main(String[] args) {
        ForkJoinPool pool = new ForkJoinPool();
        System.out.println(pool.invoke(new FibonacciTask(10)));
    }
}
```
### 동작 원리
1. 작업 분할 : 큰 작업을 재귀적으로 작은 서브태스크로 분할 (fork)
2. 작업 실행 : 각 서브태스크를 개별 Thread에서 실행
3. 결과 결합 : 서브태스크의 결과를 상위 태스크에서 결합(join)
4. Work Stealing : 유휴 Thread가 다른 Thread의 작업 큐에서 작업을 가져와 실행
> 용어 설명
> - orkJoinPool: Fork/Join 작업을 실행하기 위한 특별한 형태의 ExecutorService
> - RecursiveTask: 결과를 반환하는 Fork/Join 작업을 정의하는 클래스
> - Work Stealing: 유휴 Thread가 바쁜 Thread의 작업을 훔쳐와 실행하는 부하 분산 기법

## Thread 안정성
### 왜 Thread safety가 중요한가
- 데이터 일관성 유지
- 예측 가능한 프로그램 동작 보장
- 동시성 버그 방지
### Thread 안전성을 달성하는 방법
1. 불변 객체 사용
2. 동기화 메커니즘 사용 (synchronized, Lock등)
3. Atomic 클래스 사용
4. ThreadLocal 사용
5. Volatile 변수 사용(단일 변수의 가시성 보장)
```java
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicCounter {
    private AtomicInteger count = new AtomicInteger(0);

    public void increment() {
        count.incrementAndGet();
    }

    public int getCount() {
        return count.get();
    }
}
```
### 동작 원리
1. 메모리에서 현재 값 읽기
2. 값 증가
3. CAS(Compare-And-Swap) 연산으로 새 값 저장 시도
4. 성공하면 종료, 실패하면 1부터 재시도
> 용어 설명
> - Thread Safety: 여러 Thread가 동시에 접근해도 프로그램이 올바르게 동작하는 특성
> - Atomic Operation: 중간에 중단되지 않고 완전히 수행되거나 전혀 수행되지 않는 연산
> - CAS(Compare-And-Swap): 메모리 위치의 내용을 주어진 값과 원자적으로 비교하고, 같은 경우에만 새로운 값으로 교체하는 연산