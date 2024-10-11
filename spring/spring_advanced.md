## 로그 추적기 도입 과정
- 목표: `Controller`, `Service`, `Repository`에 변경을 최소화하여 로그 추적기 적용하기
- 로그 추적기 기본 구현
	- `TraceId`
		```java
		public class TraceId {
		    
		    private String id;
		    private int level;
		    
		    public TraceId() {
		         this.id = createId();
		         this.level = 0;
			}
			
		    private TraceId(String id, int level) {
		        this.id = id;
		        this.level = level;
		    }
		    
		    private String createId() {
		        return UUID.randomUUID().toString().substring(0, 8);
		    }
		         
		    public TraceId createNextId() {
		        return new TraceId(id, level + 1);
			}
			
		    public TraceId createPreviousId() {
		        return new TraceId(id, level - 1);
			}
		    
		    public boolean isFirstLevel() {
		        return level == 0;
			}
			
		    public String getId() {
		        return id;
			}
			
		    public int getLevel() {
		        return level;
			}
		
		}
		```
	- `TraceStatus`
		```java
		public class TraceStatus {
		    
		    private TraceId traceId;
		    private Long startTimeMs;
		    private String message;
		    
		    public TraceStatus(TraceId traceId, Long startTimeMs, String message) {
		        this.traceId = traceId;
		        this.startTimeMs = startTimeMs;
		        this.message = message;
			}
		    
		    public Long getStartTimeMs() {
		        return startTimeMs;
			}
			
		    public String getMessage() {
		        return message;
			}
		    public TraceId getTraceId() {
		        return traceId;
			}
		
		}
		```
	- `Trace` - 실제 로그 생성 및 처리
		```java
		@Slf4j
		@Component
		public class Trace {
		     
		    private static final String START_PREFIX = "-->";
		    private static final String COMPLETE_PREFIX = "<--";
		    private static final String EX_PREFIX = "<X-";
		    
		    public TraceStatus begin(String message) {
		        TraceId traceId = new TraceId();
		        Long startTimeMs = System.currentTimeMillis();
		        log.info("[{}] {}{}", traceId.getId(), addSpace(START_PREFIX, traceId.getLevel()), message);
		        return new TraceStatus(traceId, startTimeMs, message);
		    }
		     
		    public void end(TraceStatus status) {
			    complete(status, null);
		    }
		    
		    public void exception(TraceStatus status, Exception e) {
		        complete(status, e);
			}
		    
		    private void complete(TraceStatus status, Exception e) {
		        Long stopTimeMs = System.currentTimeMillis();
		        long resultTimeMs = stopTimeMs - status.getStartTimeMs();
		        TraceId traceId = status.getTraceId();
		        if (e == null) {
		            log.info("[{}] {}{} time={}ms", traceId.getId(), addSpace(COMPLETE_PREFIX, traceId.getLevel()), status.getMessage(), resultTimeMs);
		        } else {
		            log.info("[{}] {}{} time={}ms ex={}", traceId.getId(), addSpace(EX_PREFIX, traceId.getLevel()), status.getMessage(), resultTimeMs, e.toString());
				}
			}
			
		    private static String addSpace(String prefix, int level) {
		        StringBuilder sb = new StringBuilder();
		        for (int i = 0; i < level; i++) {
		            sb.append( (i == level - 1) ? "|" + prefix : "|   ");
		        }
		        return sb.toString();
		    }
		}
		```
	- 주요 `public` 메서드
		- `begin()`
		- `end()`
		- `exception()`
- 1단계: 단순 적용
	```java
	@GetMapping("/v1/request")
	public String request(String itemId) {
		TraceStatus status = null;
		try {
			status = trace.begin("OrderController.request()");
			orderService.orderItem(itemId);
			trace.end(status);
			return "ok";
		} catch (Exception e) { 
			trace.exception(status, e);
			throw e; //예외를 꼭 다시 던져주어야 한다.
		}
	}
	```
	- 해결 해야할 문제
		- **공통 로직 처리** 문제
			- **모든 컨트롤러, 서비스, 레포지토리** 핵심 로직 앞 뒤로 로그 코드를 넣어야 함 (**수작업**)
				- `begin()`, `end()`, `exception()`, `try~catch` 문
			- 로그 때문에 예외가 사라지지 않도록 **예외를 다시 던져주어야 함**
		- 로그에 대한 **문맥 정보 전달** 문제: 직전 로그 깊이와 트랜잭션 ID 전달 필요 (`TraceId`)
			- **HTTP 요청 구분** 필요 (같은 HTTP 요청이면 같은 트랜잭션 ID 남겨야 함)
			- **메서드 호출 깊이 표현** 필요 (Level)
- 2단계: 파라미터 이용한 동기화 개발
	- **문맥 정보 전달 문제 해결**
		- `Trace` 클래스에 `beginSync` 메서드 추가
			```java
			public TraceStatus beginSync(TraceId beforeTraceId, String message) {
			    TraceId nextId = beforeTraceId.createNextId();
			    Long startTimeMs = System.currentTimeMillis();
			    log.info("[" + nextId.getId() + "] " + addSpace(START_PREFIX, nextId.getLevel()) + message);
			    return new TraceStatus(nextId, startTimeMs, message);
			}
			```
		- `TraceId`를 서비스, 레포지토리 메서드 파라미터에 추가
			- `public void orderItem(TraceId traceId, String itemId) {}`
			- `public void save(TraceId traceId, String itemId) {}`
		- 각각 `TraceId` 전달해 `beginSync` 호출
	- 해결해야할 문제
		- **공통 로직 처리** 문제
			- **모든 컨트롤러, 서비스, 레포지토리** 핵심 로직 앞 뒤로 로그 코드를 넣어야 함 (**수작업**)
				- `begin()`, `end()`, `exception()`, `try~catch` 문
			- 로그 때문에 예외가 사라지지 않도록 **예외를 다시 던져주어야 함**
		- `TraceId` 동기화를 위해 **모든 관련 메서드 파라미터를 수정**해야함 (**수작업**)
- 3단계: 필드를 이용한 동기화
	- **모든 관련 메서드 파라미터 수정 문제 해결**
		- `traceIdHolder` 필드로 `TraceId` 동기화하는 `LogTrace` 구현체 개발
			```java
			@Slf4j
			public class FieldLogTrace implements LogTrace {
			     
			    private static final String START_PREFIX = "-->";
			    private static final String COMPLETE_PREFIX = "<--";
			    private static final String EX_PREFIX = "<X-";
			
				private TraceId traceIdHolder; //traceId 동기화, 동시성 이슈 발생
			    
			    @Override
			    public TraceStatus begin(String message) {
			        syncTraceId();
			        TraceId traceId = traceIdHolder;
			        ...
			        return new TraceStatus(traceId, startTimeMs, message);
				}
			    ...
			    
			    private void complete(TraceStatus status, Exception e) {
			        ...
			        releaseTraceId();
			    }
			    
			    private void syncTraceId() {
			        if (traceIdHolder == null) {
			            traceIdHolder = new TraceId();
			        } else {
			            traceIdHolder = traceIdHolder.createNextId();
			        }
				}
			    
			    private void releaseTraceId() {
			        if (traceIdHolder.isFirstLevel()) {
			            traceIdHolder = null; //destroy
			        } else {
			            traceIdHolder = traceIdHolder.createPreviousId();
					}
				}
			    ...
			
			}
			```
		- 구현체 스프링 빈 등록하면, 파라미터 전달 코드 필요 X
	- 해결해야 할 문제
		- **공통 로직 처리** 문제
			- **모든 컨트롤러, 서비스, 레포지토리** 핵심 로직 앞 뒤로 로그 코드를 넣어야 함 (**수작업**)
				- `begin()`, `end()`, `exception()`, `try~catch` 문
			- 로그 때문에 예외가 사라지지 않도록 **예외를 다시 던져주어야 함**
		- **동시성 문제**: **여러 쓰레드가 동시에** 같은 인스턴스의 필드 값을 **변경**하면서 발생하는 문제
			- **싱글톤 스프링 빈** `FieldLogTrace` 인스턴스는 **애플리케이션에 딱 1개** 존재
			- 동시에 여러 사용자가 요청하면, **여러 스레드가 `traceIdHolder` 필드에 동시 접근**
			- 트래픽이 적은 상황에서는 확률상 잘 나타나지 않고, **트래픽이 많아질수록 자주 발생**
- 4단계: 필드 동기화 - **스레드 로컬(ThreadLocal)** 적용
	- **싱글톤 객체 필드**를 사용할 때 **동시성 문제 해결**
		- `traceIdHolder` 필드가 스레드 로컬을 사용하도록 변경
			- `TraceId traceIdHolder` -> **`ThreadLocal<TraceId> traceIdHolder`**
			- `private ThreadLocal<TraceId> traceIdHolder = new ThreadLocal<>();`
		- 값을 저장할 때는 `set(...)`, 조회할 때는 `get()` 사용
			- `traceIdHolder.set(new TraceId());`
			- `TraceId traceId = traceIdHolder.get();`
		- 호출 추적 로그 완료 시, 반드시 **`remove()`** 호출 (**스레드 전용 보관소 내 값 제거**)
	- 해결해야 할 문제
		- **공통 로직 처리** 문제
			- **모든 컨트롤러, 서비스, 레포지토리** 핵심 로직 앞 뒤로 로그 코드를 넣어야 함 (**수작업**)
				- `begin()`, `end()`, `exception()`, `try~catch` 문
			- 로그 때문에 예외가 사라지지 않도록 **예외를 다시 던져주어야 함**
## 스레드 로컬(ThreadLocal)
- 일반적인 공유 변수 필드 (문제)
	- **여러 스레드**가 같은 인스턴스의 필드에 접근하면 **처음 스레드가 보관한 데이터가 사라질 수 있음**
- **스레드 로컬 필드** (**해결**)
	![java_threadlocal_inner_logic](../images/java_threadlocal_inner_logic.png)
	![java_thread_local](../images/java_thread_local.png)
	- **각 스레드마다 제공**되는 **별도의 내부 저장소** (**본인 스레드만 접근 가능**)
		- **여러 스레드**가 **같은 인스턴스의 스레드 로컬 필드에 접근**해도 **문제 X**
			- 정말 **완전히 동시에 들어와도 구분** 가능
		- **각각의 스레드 객체**는 자신만의 **`ThreadLocalMap`** 을 가짐 (**전용 보관소**)
			- 키: `ThreadLocal` 인스턴스 참조 (e.g. `nameStore`) / 값: 데이터 (e.g. `userA`)
			- 참고로 스레드 로컬 저장소와 이에 보관된 데이터들은 힙 영역에 저장됨
	- 스프링 빈 같은 **싱글톤 객체 필드**를 사용하면서도 **동시성 문제 해결 가능**
		- **일반적으로** Controller, Service **싱글톤 빈**들에는 **상태값 필드를 두지 않음** (동시성 문제 예방)
		- **상태값을 저장해야 하는 경우**에만 **스레드 로컬**로 해결
	- **`java.lang.ThreadLocal`** 클래스 (자바 지원)
		- 변수 정의: `private ThreadLocal<String> nameStore = new ThreadLocal<>();`
		- 저장: `nameStore.set(name);`
		- 조회: `nameStore.get()`
		- 제거: `nameStore.remove()`
	- 그림 시나리오
		- `thread-A`가 `userA` 값 **저장** 시 **스레드 로컬**은 `thread-A` 전용 보관소에 데이터 보관
		- `thread-B`가 `userB` 값 **저장** 시 **스레드 로컬**은 `thread-B` 전용 보관소에 데이터 보관
		- `thread-A`가 **조회** 시 **스레드 로컬**은 `thread-A` 전용 보관소에서 `userA` 데이터 반환
		- `thread-B`가 **조회** 시 **스레드 로컬**은 `thread-B` 전용 보관소에서 `userB` 데이터 반환
	- 유의사항
		- 스레드는 스레드 로컬 **사용완료** 후 **스레드 로컬에 저장된 값을 항상 제거해야 함** (**`remove()`**)
			- 스레드 전용 보관소가 아니라 **스레드 전용 보관소 내 값 제거**
			- 즉, **요청이 끝날 때**
				- **필터나 인터셉터에서 clear**하거나
				- **최소한 `ThreadLocal.remove()` 반드시 호출할 것**
		- **제거하지 않을 경우 문제** 발생
			- **스레드 풀 없는 상황**에서는 가비지 컬렉터가 회수할 수 없어 **메모리 누수 발생 가능**
			- **WAS(톰캣)**처럼 **스레드 풀 사용하는 경우 문제** 발생!
				![threadlocal_scenario_1](../images/threadlocal_scenario_1.png)
				![threadlocal_scenario_2](../images/threadlocal_scenario_2.png)
				![threadlocal_scenario_3](../images/threadlocal_scenario_3.png)
				- `thread-A`가 풀에 반환될 때, `thread-A` **전용 보관소에 데이터 남아있음**
				- 스레드 풀 스레드는 **재사용**되므로, 사용자 B 요청도 `thread-A` 할당 받을 수 있음
				- 결과적으로, **사용자B가 사용자A의 데이터를 확인**하게 되는 **심각한 문제**가 발생
				- 따라서, 사용자A의 **요청이 끝날 때 `remove()` 필요**

***
## Reference
[스레드 로컬 (Thread Local)](https://inma.tistory.com/171)