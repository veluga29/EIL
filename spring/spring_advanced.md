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
