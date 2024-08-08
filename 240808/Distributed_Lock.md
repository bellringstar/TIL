# Distributed Lock
분산 락은 여러 서버에 걸처 동시성을 제어하는 핵심 메커니즘입니다.

- 분산 락이 선착순 이벤트에서 작동하는 방식
  - 사용자가 쿠폰을 요청할 때, 서버는 먼저 분산 락을 획득합니다.
  - 락을 획득한 서버만 쿠폰 발급 로직을 실행할 수 있습니다.
  - 쿠폰 발급 후 락을 해제하여 다른 서버가 처리할 수 있게 합니다.
- 우리의 목표에 도움이 되는 이유
  - 여러 서버에서 동시에 같은 쿠폰을 발급하는 것을 방지합니다.
  - 정확한 수량의 쿠폰만 발급되도록 보장합니다.
  - 시스템의 일관성을 유지하여 사용자에게 공정한 기회를 제공합니다.
- 상세 설명
  - 분산 락의 기본 요구사항
    - 상호 배제(Mutual Exclusion): 한 번에 하나의 클라이언트만 락을 획들할 수 있어야 합니다.
      - 상호 배제는 임계 구역에 동시에 하나의 프로세스만 접근할 수 있도록 보장하는 것입니다. 분산 시스템에서는 이를 네트워크를 통해 여러 노드 간에 구현해야 합니다.
        - 글로벌 락 관리자
          - 중앙화된 락 관리자를 두어 모든 락 요청을 처리합니다.
          - 장점: 구현이 단순하고 직관적입니다.
          - 단점: 단일 실패 지점이 될 수 있습니다.
        - 분산 합의 알고리즘
          - Paxos나 Raft와 같은 알고리즘을 사용하여 락 상태에 대한 합의를 이룹니다.
          - 장점: 높은 신뢰성과 일관성을 제공합니다.
          - 단점: 구현이 복잡하고 오버헤드가 있을 수 있습니다.
        - 시간 기반 락
          - 각 노드가 타임스탬프를 사요하여 락 획득을 시도합니다.
          - Lamport 타임스탬프나 벡터 클록을 사용할 수 있습니다.
          - 장점: 확장성이 좋고 중앙화된 조정자가 필요 없습니다.
          - 단점: 시계 동기화 문제가 발생할 수 있습니다.
    - 교착 상태 방지(Deadlock Free): 클라이언트가 크래시되어도 다른 클라이언트가 락을 획득할 수 있어야 합니다.
      - 교착 상태는 두 개 이상의 프로세스가 서로 보유한 자원을 기다리며 무한정 대기하는 상황입니다. 분산 락 시스템에서는 이를 방지해야 합니다.
        - 타임아웃 메커니즘
          - 락 획득 시도에 제한 시간을 둡니다.
          - 장점: 구현이 간단하고 효과적입니다.
          - 단점: 적절한 타임아웃 값 설정이 어려울 수 있습니다.
        - 자원 순서화
          - 모든 프로세스가 정해진 순서대로 자원을 요청하도록 합니다.
          - 예: 항상 낮은 ID의 락부터 획득하도록 규칙을 정합니다.
          - 장점: 교착 상태를 근본적으로 방지할 수 있습니다.
          - 단점: 모든 시스템 검포넌트가 이 규칙을 따라야 하므로 구현이 복잡할 수 있습니다.
        - 탐지 및 복구
          - 주기적으로 시스템의 상태를 검사하여 교착 상태를 탐지합니다.
          - 탐지 시 하나 이상의 프로세스를 강제 종료하여 교차 상태를 해결합니다.
          - 장점: 다양한 상황에 유연하게 대응할 수 있습니다.
          - 단점: 오버헤드가 크고 강제 종료된 프로세스의 복구 로직이 필요합니다.
    - 내결함성(Fault Tolerance): 락 서버의 일부가 실패해도 시스템이 계속 작동해야 합니다.
      - 내결함성은 시스템의 일부 구성 요소가 실패하더라도 전체 시스템이 계속 작동할 수 있는 능력입니다. 분산락 시스템에서는 노드 실패, 네트워크 파티션 등의 상황에서도 일관성과 가용성을 유지해야 합니다.
        - 복제(Replication)
          - 락 정보를 여러 노드에 복제합니다.
          - 장점: 단일 노드 실패에 대한 내성이 생깁니다.
          - 단점: 복제 노드 간 일관성 유지가 필요합니다.
        - 리더 선출(Leader Election)
          - Zookeeper의 ZAB, etcd의 Raft등의 알고리즘을 사용합니다.
          - 장점: 강력한 일관성을 제공합니다.
          - 단점: 구현이 복잡하고 오버헤드가 있습니다.
        - 쿼럼 기반 법근(Quorum-based Approach)
          - 과반수의 노드가 동의할 때만 락 획득/해제를 인정합니다.
          - 장점: 네트워크 파티션 상황에서도 안정성을 보장합니다.
          - 단점: 성능 오버헤드가 있을 수 있습니다.
  - 분산 락 구현 방법
    - 데이터베이스를 이용한 구현
      - 장점: 기존 인프라 활용 가능, 트랜잭션과 통합 용이
      - 단점: 확장성 제한, 데이터베이스 부하 증가
      - 락 테이블 생성
        - 데이터베이스에 락 정보를 저장할 전용 테이블을 생성합니다. 이 테이블은 락의 상태를 추적하고 관리하는데 사용됩니다.
          - ```sql
            CREATE TABLE distributed_locks (
                lock_name VARCHAR(255) PRIMARY KEY,
                lock_holder VARCHAR(255),
                acquired_at TIMESTAMP,
                expires_at TIMESTAMP,
                INDEX idx_expires_at (expires_at)
            );
            ```
          - 테이블 구조
            - lock_name: 락의 식별자
            - lock_holder: 락을 소유한 프로세스 또는 서버의 식별자
            - acquired_at: 락 획득 시간
            - expires_at: 락 만료 시간
          - 인덱스 설계
            - lock_name에 대한 PRIMARY KEY 인덱스는 빠른 락 조회와 동시성 제어를 제공합니다.
            - expires_at에 대한 보조 인덱스는 만료된 락을 효율적으로 정리하는 데 사용됩니다.
          - 락 격리 수준
            - SERIALIZABLE 격리 수준을 사용하여 동시성 문제를 방지합니다.
            - 일부 데이터베이스에서는 SELECT ... FOR UPDATE 구문을 사용하여 행 수준 잠금을 구현할 수 있습니다.
      - 락 획득 메커니즘
        - 락 획득은 락 테이블에 새로운 행을 삽입하거나 기존 행을 업데이트하는 방식으로 구현됩니다. 이 과정에서 동시성 제어가 중요합니다.
        - 삽입 기반 락 획득
            ```sql
            INSERT INTO distributed_locks (lock_name, lock_holder, acquired_at, expires_at)
            VALUES ('resource_1', 'process_1', NOW(), NOW() + INTERVAL 30 SECOND)
            ON DUPLICATE KEY UPDATE
                lock_holder = IF(expires_at < NOW(), VALUES(lock_holder), lock_holder),
                acquired_at = IF(expires_at < NOW(), VALUES(acquired_at), acquired_at),
                expires_at = IF(expires_at < NOW(), VALUES(expires_at), expires_at);
            ```
          - 이 쿼리는 락이 존재하지 않거나 만료된 경우에만 락을 획득합니다.
          - DUPLICATE KEY 처리를 통해 동시 요청을 안전하게 처리합니다.
        - 조건부 업데이트 기반 락 획득
          ```sql
          UPDATE distributed_lock
          SET lock_holder = 'process_1',
              acquired_at = NOW(),
              expires_at = NOW() + INTERVAL 30 SECOND
          WHERE lock_name = 'resource_1'
            AND (lcok_holder IS NULL OR expires_at < NOW());
          ```
          - 이 방식은 업데이트된 행의 수를 확인하여 락 획득 성공 여부를 판단합니다.
        - 재시도 메커니즘
          - 락 획득 실패 시 지수 백오프 알고리즘을 사용하여 재시도합니다.
      - 락 해제 메커니즘
        - 락 해제는 락 테이블에서 해당 락 정보를 삭제하거나 만료 시간을 과거로 설정하는 방식으로 구현합니다.
          - 삭제 기반 락 해제
            - ```sql
              DELETE FROM distributed_locks
              WHERE lock_name = 'resource_1' AND lock_holder = 'process_1';
              ```
            - 이 방식은 락 정보를 완전히 제거합니다.
            - 장점: 테이블 크기를 작게 유지할 수 있습니다.
            - 단점: 락 히스토리를 추적하기 어렵습니다.
          - 업데이트 기반 락 해제
            - ```sql
              UPDATE distributed_locks
              SET expires_at = NOW()
              WHERE lock_name = 'resource_1' AND lock_holder = 'process_1';
              ```
            - 이 방식은 락을 즉시 만료시킵니다.
            - 장점: 락 히스토리를 유지할 수 있습니다.
            - 단점: 주기적인 테이블 정리가 필요합니다.
          - 페이싱 기법(Fencing)
            - 락 해제 시 페이싱 토큰을 증가시켜 오래된 락 홀더의 작업을 방지합니다.
            - ```sql
              UPDATE distributed_locks
              SET expires_at = NOW(), fencing_token = fencing_token + 1
              WHERE lock_name = 'resource_1' AND lcok_holder = 'process_1';
              ```
      - 락 만료 처리
        - 분산 환경에서는 락을 획득한 프로세스가 예기치 않게 종료도리 수 있습니다. 이를 대비해 락 만료 메커니즘을 구현해야 합니다.
        - 자동 만료
          - expires_at 컬럼을 활용하여 락의 유효 기간을 설정합니다.
          - 새로운 락 획득 시도 시 만료된 락을 자동으로 갱신합니다.
        - 백그라운드 정리 작업
          - ```sql
            DELETE FROM distributed_locks WHERE expires_at < NOW();
            ```
          - 이 쿼리를 주기적으로 실행하여 만료된 락을 정리합니다.
          - 주의: 이 작업은 테이블 전체 스캔을 유발할 수 있으므로 인덱스 사용이 중요합니다.
        - 락 생신
          - 장기 실행 작업의 경우, 주기적으로 락의 만료 시간을 연장합니다.
          - ```sql
            UPDATE distributed_locks
            SET expires_at = NOW() + INTERVAL 30 SECOND
            WHERE lock_name = 'resource_1' AND lock_holder = 'process_1';
            ```
      - 성능 최적화
        - 데이터베이스를 사용한 분산 락은 간단하지만 성능 병목이 될 수 있습니다. 따라서 다양한 최적화 기법을 적용해야 합니다.
        - 연결 풀링
          - 데이터베이스 연결을 재사용하여 연결 오버헤드를 줄입니다.
          - HikariCP나 c3p0 같은 연결 풀 라이브러리를 사용합니다.
        - 배치 처리
          - 여러 락 작업을 하나의 트랜잭션으로 묶어 처리합니다.
        - 읽기/쓰기 분리
          - 락 확인과 획득을 분리하여 읽기 전용 복제본을 활용합니다.
          - 읽기 작업에 대해 낮은 격리 수준을 사용하여 성능을 향상시킵니다.
        - 샤딩
          - 락 이름을 기준으로 여러 데이터베이스 서버에 분산하여 부하를 분산시킵니다.
          - 주의: 샤딩 환경에서의 글로벌 락 관리에 주의가 필요합니다.
        - 캐싱
          - 자주 사용되는 락 정보를 메모리에 캐시하여 데이터베이스 조회를 줄입니다.
          - 캐시 일관성 유지에 주의가 필요합니다.
    - Redis를 이용한 구현
      - 장점: 고성능, 확장성 우수
      - 단점: 추가 인프라 필요, 운영 복잡도 증가
      ```java
        // redisson 라이브러리 사용 예시
      RLock lock = redissonClient.getLock("coupon_event");
      try {
          if (lock.tryLock(10, 30, TimeUnit.SECONDS)) {
          }
      } finally {
          lock.unlock();
      }
      ```
      - Redis 분산 락의 기본 원리
        - Redis는 싱글 스레드 모델과 원자적 연산을 제공하여 분산 락 구현에 적합합니다.
          - 키-값 저장소 활용
            - 락은 Redis의 키로 표현되며, 값은 락 소유자 정보를 담습니다.
            - 예: SET resource_name unique_value NX PX 30000
          - 원자성 보장
            - Redis의 단일 명령어는 원자적으로 실행됩니다.
            - 복잡한 연산은 Lua 스크립트를 통해 원자성을 보장할 수 있습니다.
          - 만료 시간 설정
            - Redis의 key expiration 기능을 활용하여 락의 수명을 제어합니다.
      - 락 획득 메커니즘
        - SET 명령어와 NX (Not Exists) 옵션을 사용하여 락을 획득하빈다.
          - 기본 락 획득 : SET resource_name unique_value NX PX 30000
            - unique_value: 락 소유자를 식별하는 고유값 (예: UUID)
            - NX: 키가 존재하지 않을 때만 설정
            - pX 30000: 30초 후 자동 만료
          - 재시도 메커니즘
          ```java
          String lockKey = "resource_name";
          String lockValue = UUID.radomUUID().toString();
          long retryDelay = 100;
          
          while (!redisTemplate.opsForValue().setIfAbsent(lockKey, lockValue, 30, TimeUnit.SECONDS)) {
              Thread.sleep(retryDelay);
              retryDelay = Math.min(retryDelay * 2, MAX_RETRY_DELAY);
          }
          ```
          - 락 연장
          ```lua
          if redis.call("get", KEY[1]) == ARGV[1] then
              return redis.call("pexpire", KEYS[1], ARGV[2])
          else
              return 0
          end
          ```
            - 이 Lua 스크립트는 현재 락 소유자만 락을 연장할 수 있도록 합니다.
      - 락 해제 메커니즘
        - DEL 명령어를 상요하여 락을 해제하지만, 락 소유자 확인이 필요합니다.
          - 안전한 락 해제
          ```lua
          if redis.call("get", KEYS[1]) == ARGV[1] then
              return redis.call("del", KEYS[1])
          else
              return 0
          end
          ```
          - 이 Lua 스크립트는 현재 락 소유자만 락을 해제할 수 있도록 합니다.
          - 클라이언트 측 구현
          ```java
          public boolean releaseLock(String lockKey, String lockValue) {
              DefaultRedisScript<Long> script = new DefaultRedisScript<>();
              script.setScriptText("if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end");
              script.setResultType(Long.class);
          
              Long result = redisTemplate.execute(script, Collections.singletonList(lockKey));
              return result != null && result == 1;
          }
          ```
      - 안전성 보장
        - Redis 분산 락은 일부 시나리오에서 안전성 문제가 발생할 수 있습니다.
          - 시계 드리프트 문제
            - 서버 간 시간 차이로 인해 락의 실제 수명이 예상과 다를 수 있습니다.
            - 해결책: NTP를 사용한 시간 동기화, 보수적인 만료 시간 설정
          - 단일 Redis 인스턴스 장애
            - 단일 Redis 서버 사용 시 해당 서버 장애로 전체 시스템이 영향을 받습니다.
            - 해결책: Redis Sentinel 또는 Redis Cluster 사용
          - 네트워크 파티션
            - 네트워크 분할 시 여러 클라이언트가 동시에 락을 소유했다고 믿을 수 있습니다.
            - 해결책: Redlock 알고리즘 사용(다중 Redis 인스턴스에 걸친 락 획득)
      - 성능 최적화
        - Redis는 이미 고성능이지만 추가적인 최적화가 가능합니다.
          - 파이프라이닝
            - 여러 명령을 한 번에 보내 네트워크 왕복 시간을 줄입니다.
            ```java
            redisTemplate.executePipelined((RedisCallback<Object>) connection -> {
                connection.setNX(key1.getBytes(), value1.getBytes());
                connection.setNX(key2.getBytes(), value2.getBytes());
                return null;
            });
            ```
          - 연결 풀링
            - Lettuce 또는 Jedis 클라이언트의 연결 풀을 적절히 구성합니다.
          - Redis Cluster
            - 락을 여러 Redis 노드에 분산하여 부하를 분산시킵니다.
      - 고급 기능 및 패턴
        - 재진입 가능한 락
        ```lua
        local token = redis.call('get', KEYS[1])
        if token == ARGV[1] then
            redis.call('incr', KEYS[1] .. ":count")
            redis.call('expire', KEYS[1], ARGV[2])
            return 1
        elseif not token then
            redis.call('set', KEYS[1], ARGV[1], 'PX', ARGV[2])
            redis.call('set', KEYS[1] .. ":count", 1)
            return 1
        else
            return 0
        end
        ```
        - 공정성 보장
          - redis의 sorted set 을 사용하여 FIFO 큐를 구현합니다.
          - ZADD lock_queue <current_timestamp> <client_id>
        - 락 획득 대기열
          - Redis의 List를 사용하여 대기 중인 클라이언트를 관리합니다.
          - LPUSH lock_wait_queue <client_id>
    - Zookeeper를 이용한 구현
      - 장점: 강력한 일관성, 복잡한 분산 시나리오 처리 가능
      - 단점: 복잡한 설정, 추가 인프라 필요
  - 분산 락의 고급 주제
    - 락 재시도 메커니즘
      - 기본 재시도 전략
        - 락 획득 실패 시 일정 시간 후 다시 시도합니다.
        - 고정 간격 재시도
          - 장점: 구현이 간단합니다.
          - 단점: 모든 클라이언트가 동시에 재시도할 가능성이 높아 경합이 심해질 수 있습니다.
        - 랜덤 간격 재시도
          - Thread.sleep(ThreadLocalRandom.current().nextLong(MIN_RETRY_INTERVAL, MAX_RETRY_INTERVAL));
          - 장점: 클라이언트 간 재시도 시간을 분산시켜 경합을 줄입니다.
          - 단점: 완전한 램덤은 예측 가능성이 낮습니다.
      - 지수 백오프
        - 재시도 간격을 점진적으로 증가시켜 시스템 부하를 줄입니다.
        - 기본 구현
          ```java
          long retryDelay = INITIAL_RETRY_DELAY;
          for (int i = 0; i < MAX_RETRIES; i++) {
              if (tryLock(resourceName)) {
              Thread.sleep(retryDelay);
              retryDelay = Math.min(retryDelay * 2, MAX_RETRY_DELAY);
              }
          }
          ```
          - 장점: 초기에 빠른 재시도, 이후 점진적으로 시스템에 부하를 줄입니다.
          - 단점: 여전히 여러 클라이언트가 동기화된 패턴으로 재시도할 수 있습니다.
        - 지수 백오프 계산
        ```java
        long calculateBackoff(int attempt) {
            return (long) Math.pow(2, attempt) * BASE_DELAY;
        }
        ```
      - 지터(Jitter) 추가
        - 재시도 간격에 임의성을 추가하여 클라이언트 간 재시도 시간을 더욱 분산시킵니다.
        - 전체 지터
          ```java
          long calculateBackoffWithJitter(int attempt) {
              long baseDelay = calculateBackoff(attempt);
              return ThreadLocalRandom.current().nextLong(0, baseDelay);
          }
          ```
          - 장점: 클라이언트 간 재시도 시간을 효과적으로 분산시킵니다.
          - 단점: 때때로 매우 짧은 재시도 간격이 발생할 수 있습니다.
        - 평등 지터
          ```java
          long calculateBackoffWithEqualJitter(int attempt) {
              long baseDelay = calculateBackoff(attempt);
              return baseDelay / 2 + ThreadLocalRandom.curretn().nextLond(0, baseDelay / 2);
          }
          ```
          - 장점: 최소 대기 시간을 보장하면서도 충분한 임의성을 제공합니다.
        - 장식 지터
          ```java
          long calculateDecorrelatedJitter(long previousDelay) {
              return Math.min(MAX_RETRY_DELAY,
                     ThreadLocalRandom.current().nextLong(BASE_DELAY, previousDelay * 3)
              );
          }
          ```
          - 장점: 이전 대기 시간을 기반으로 다음 대기 시간을 계산하여 더 나은 분산을 제공합니다.
      - 재시도 제한 및 타임아웃
        - 무한 재시도를 방지하고 전체 작업 시간을 제한합니다.
        - 최대 재시도 횟수
          - ```java
            public boolean acquireLockWithLimit(String resourceName) {
                for (int attempt = 0; attempt < MAX_RETRIES; attempt++){
                    if (tryLock(resourceName)) {
                        return true;
                    }
                    Thread.sleep(calculateBackoffWithJitter(attempt));
                }
                throw new LockAcquisitionException("Failed to acquire lock after " + MAX_RETRIES + " attempts");
            }
            ```
        - 전체 타임아웃
          - ```java
            public boolean acquireLockWithTimeout(String resourceName, long timeout) {
                long startTime = System.currentTimeMillis();
                long elapsedTime;
                int attempt = 0;
                
                do {
                    if (tryLock(resourceName)) {
                        return true;
                    }
                    Thread.sleep(calculateBackoffWithJitter(attempt++)) {
                    elapsedTime = System.currentTimeMillis() - startTime;
                    }
                } while (elapsedTime < timeout);
            
                return false;
            }
            ```
      - 회로 차단기 패턴 (Circuit Breaker Pattern)
        - 연속된 실패를 감지하여 일정 기간 동안 재시도를 중단합니다.
        - 상태 기반 회로 차단기
          - ```java
            public class LockCircuitBreaker {
                private int failureCount = 0;
                private long lastFailureTime = 0;
                private static final int FAILURE_THRESHOLD = 5;
                private static final long RESET_TIMEOUT = 60000;
            
                public boolean allowRequest() {
                    if (failureCount >= FAILURE_THRESHOLD) {
                        if (System.currentTimeMillis() - lastFailureTime > RESET_TIMEOUT) {
                            reset();
                            return true;
                        }
                        return false;
                    }
                    return true;
                }
            
                public void recordSuccess() {
                    reset();
                }
            
                public void recordFailure() {
                    failureCount++;
                    lastFailureTime = System.currentTimeMillis();
                }   
                
                public void reset() {
                    failureCount = 0;
                    lastFailureTime = 0;
                }
            }
            ```
            - 락 획득 실패가 일시적인 문제인지 지속적인 문제인지 구분하는 데 도움을 줍니다.
      - 우선순위 기반 재시도
        - 작업의 중요도나 긴급성에 따라 재시도 전략을 다르게 사용합니다.
        - 우선순위 큐 사용
          - ```java
            public class PriorityRetryScheduler {
                private PriorityQueue<RetryTask> queue = new PriorityQueue<>();
                
                public void scheduleRetry(String resourceName, int priority) {
                    queue.offer(new RetryTask(resourceName, priority, System.currentTimeMillis()));
                }
            
                public RetryTask getNextRetry() {
                    return queue.poll();
                }
            
                private class RetryTask implements Comparable<RetryTask> {
                    String resourceName;
                    int priority;
                    long timestamp;
                }
            }
            ```
          - 중요한 작업이 더 빨리, 더 자주 재시도될 수 있습니다.
        - 동적 우선순위 조정
          - ```java
            public void adjustPriority(String resourceName, long waitTime) {
                int newPriority = calculateDynamicPriority(waitTime);
                // update priority in the queue
            }
            
            private int calculateDynamicPriority(long waitTime) {
                return (int) (BASE_PRIORITY + (waitTime / WAIT_TIME_FACTOR));
            }
            ```
          - 대기 시간이 길어질수록 작업의 우선순위를 높여 기아 상태를 방지합니다.
    - 락 타임아웃 처리
      - 자동 해제 메커니즘 구현
      - 락 갱신(Lock Renewal) 전략 사용
      ```java
      ScheduledExecutorService scheduler = Executors.newSingleThreadScheduledExecutor();
      scheduler.scheduleAtFixedRate(() -> {
          if (lock.isHeldByCurrentThread()) {
              lock.extend(30, TimeUnit.SECONDS);
          }
      }, 10, 10, TimeUnit.SECONDS);
      ```
    - 락 세분화
      - 리소스별로 별도의 락 사용
      ```java
      RLock lock = redissonClient.getLock("coupon_event_" + couponId);
      ```
    - 분산 락의 성능 최적화
      - 락 획득 시간 모니터링
      - 락 경합 분석 및 최적화
      - 락 획득 대기열 구현
  - 분산 락의 이론적 배경
    - 분산 합의 알고리즘
      - Paxos 알고리즘
      - Raft 알고리즘
    - CAP 정리와 분산 락의 관계
      - 일관성과 가용성 사이의 트레이드오프
  - 분산 락 테스트 및 디버깅
    - 단위 테스트 작성
    - 통합 테스트 시나리오 구성
    - 카오스 엔지니어링 기법 적용

목표
1. 정확성: 지정된 수량만큼 정확히 쿠폰을 발급할 수 있습니다.
2. 확장성: 서버를 늘려도 일관된 동작을 보장합니다.
3. 성능: 최적화된 락 구현으로 높은 처리량을 달성할 수 있습니다.
4. 신뢰성: 장애 상황에서도 시스템이 올바르게 동작합니다.