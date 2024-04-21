## 로깅 (Logging)
- 스프링 부트 로깅 라이브러리(`spring-boot-starter-logging`)에서 다음 로깅 라이브러리 사용 
	- SLF4J 라이브러리: 로그 라이브러리를 통합해서 **인터페이스**로 제공 (Logback, Log4J, Log4J2...)
	- Logback 라이브러리: 실무에서 **구현체** 로그 라이브러리로 주로 사용 (스프링 부트 기본 제공)
- 로그 선언
	- 기본 사용법
		- `private static final Logger log = LoggerFactory.getLogger(getClass());`
	- 롬복 사용
		- `@Slf4j`
		- 다음 코드를 자동 생성
			- `private static final Logger log = LoggerFactory.getLogger(Xxx.class);`
- 올바른 로그 사용법
	- 올바른 사용법: `log.debug("data={}", data)`
	- 잘못된 사용법: `log.debug("String concat log=" + name)`
		- 로그 출력 레벨을 info로 설정하면 로그 남지 않으나, **더하기 연산은 무조건 수행되어 비효율**
- 로그 출력 포멧
	- 시간 / 로그 레벨 / 프로세스 ID / 쓰레드 명 / 클래스명 / 로그 메시지
- 로그 레벨 수준 (정보가 많은 순서로)
	- TRACE > DEBUG > INFO > WARN > ERROR
	- **DEBUG**: **개발 서버** 적합
	- **INFO**: **운영 서버** 적합
- 로그 레벨별 호출 방법
	```java
	//@Slf4j
	@RestController
	public class LogTestController {
	
	private final Logger log = LoggerFactory.getLogger(getClass());
	
	    @RequestMapping("/log-test")
	    public String logTest() {
	        String name = "Spring";
	        
	        log.trace("trace log={}", name);
	        log.debug("debug log={}", name);
	        log.info(" info log={}", name);
	        log.warn(" warn log={}", name);
	        log.error("error log={}", name);
			
			return "ok";
	    }
	}
	```
- **로그 레벨 전역 설정** (**`application.properties`**)
	- 전체 로그 레벨 설정 (디폴트: info)
		- `logging.level.root=info`
	- 특정 패키지 및 하위 패키지 로그 레벨 설정
		- `logging.level.hello.springmvc=debug`
- 장점
	- 쓰레드 정보, 클래스 이름 같은 부가 정보 확인 가능
	- 출력 포멧 조절 가능
	- 로그 레벨 설정으로 상황에 맞게 조절 가능
	- 파일, 네트워크 등 콘솔 뿐만 아니라 별도의 위치에 남길 수 있음 (파일의 경우 일별, 용량별 분할 가능)
	- System.out 보다 성능도 좋음

***
## Reference
*[스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-1#)*