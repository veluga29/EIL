## 스프링 프로젝트 세팅 방법
1. 프로젝트 GENERATE: https://start.spring.io
	- Spring Boot Version은 SNAPSHOT, M2가 들어가지 않은 것이 정식 버전
	- Package name은 `-`가 안들어가도록 주의
	- `ADD DEPENDENCIES`: Spring WEB, Thymeleaf, Lombok, Validation, JPA, H2...
2. Settings
	- Lombok
		- `Plugins` - `Lombok` 설치
		- `Annotation Processing` - `Enable annotation processing`
	- `Gradle` - `Build and run using, Run tests using` - `IntelliJ IDEA` 변경
3. Main 함수 실행 - White label page 확인
## H2 Database 세팅 방법
- 설치 - [H2 Database](https://www.h2database.com)
- 데이터베이스 파일 생성 (첫 진입)
	- `jdbc:h2:~/jpashop`
	- 다음 파일 생성 확인: `~/jpashop.mv.db`
- 이후부터 TCP 연결
	- `jdbc:h2:tcp://localhost/~/jpashop`
## JPA 및 DB 설정
- `main/resources/application.yml`
	```yml
	spring:  
	  datasource:  
	    url: jdbc:h2:tcp://localhost/~/jpashop  
	    username: sa  
	    password:  
	    driver-class-name: org.h2.Driver  
	  
	  jpa:  
	    hibernate:  
	      ddl-auto: create  
	    properties:  
	      hibernate:  
	#        show_sql: true  # System.out을 통해 SQL 남김 (지양)
	        format_sql: true  
	  
	logging.level:  
	  org.hibernate.SQL: debug  
	  org.hibernate.orm.jdbc.bind: trace
	```
- `ddl-auto`
	- `create`: 애플리케이션 실행 시점에 테이블을 drop하고 다시 생성
	- `none`: 테이블을 생성하지 않음
- `org.hibernate.SQL`
	- logger를 통해 SQL 남김
- `org.hibernate.orm.jdbc.bind: trace`
	- SQL 실행 파라미터(쿼리 파라미터)를 로그로 남김
- 외부 라이브러리 (가독성 높은 쿼리 파라미터 로그)
	- `implementation 'com.github.gavlyukovskiy:p6spy-spring-boot-starter:1.9.0'`
	- 커넥션 정보, 가독성 높은 쿼리 파라미터 등 상세 정보 제공
	- 시스템 자원을 잡아 먹으므로 **운영 시스템에 적용하려면 반드시 성능 테스트 필요** (개발 단계 자유 사용)
## Test 설정 파일
```yml
spring:
 
logging.level:
  org.hibernate.SQL: debug
```
- 경로: `test/resources/application.yml`
## 유용한 명령어
- 의존관계 확인 (Tree view)
	- 프로젝트 디렉토리 - `./gradlew dependencies -configuration compileClasspath`
- 서버 재시작 없이 View 파일 변경하기
	- `spring-boot-devtools` 라이브러리 추가
	- html 파일만 컴파일 (`build` - `Recompile`)
## 초기 데이터 생성
```java
@Slf4j
@RequiredArgsConstructor
public class TestDataInit {

	private final ItemRepository itemRepository;
	
	/**
	* 확인용 초기 데이터 추가 
	*/
	@EventListener(ApplicationReadyEvent.class)
	public void initData() {
		log.info("test data init");
		itemRepository.save(new Item("itemA", 10000, 10));
		itemRepository.save(new Item("itemB", 20000, 20));
	} 
}
```
- **`@EventListener(ApplicationReadyEvent.class)`**
	- 스프링 컨테이너가 완전히 초기화를 끝내고, **실행 준비가 되었을 때** 발생하는 이벤트
	- **스프링 컨테이너가 AOP를 포함해 완전히 초기화된 시점**
		- `@PostConstruct`의 경우, AOP 같은 부분이 다 처리되지 않은 시점에 호출될 수 있음
		- 예를 들어, `@Transactional` 관련 AOP가 적용되지 않고 호출될 수 있어 문제
	- 스프링은 이 시점에, `initData()`를 호출
## 프로필 (Profile)
- 프로필은 로컬, 운영 환경, 테스트 실행 등 **다양한 환경에 따라 다른 설정을 할 때 사용하는 정보**
	- 로컬에서는 로컬 DB, 운영 환경에서는 운영 DB에 접근
	- 환경에 따라 다른 스프링 빈 등록
- 스프링은 로딩 시점에 **`spring.profiles.active`** 사용 (`spring.profiles.active=local`)
	- main 프로필: **`src/main/resources`** 하위 **`application.properties`**
	- test 프로필: **`src/test/resources`** 하위 **`application.properties
- 프로필을 지정하지 않으면 `"default"` 프로필로 동작
## 설정파일(Config) 및 프로필 적용하기
```java
@Import(MemoryConfig.class)
@SpringBootApplication(scanBasePackages = "hello.itemservice.web")
public class ItemServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(ItemServiceApplication.class, args);
	}
	
	@Bean
	@Profile("local")
	public TestDataInit testDataInit(ItemRepository itemRepository) {
		return new TestDataInit(itemRepository);
	}
}
```
- **`@Import(MemoryConfig.class)`**
	- 원하는 설정파일 적용
- **`@Profile("local")`**
	- 특정 프로필의 경우에만 해당 스프링 빈 등록
## 주요한`application.properties` 설정
- 트랜잭션 프록시가 호출하는 트랜잭션의 시작 및 종료 로그 확인 가능
	- `logging.level.org.springframework.transaction.interceptor=TRACE`
	- `logging.level.org.springframework.jdbc.datasource.DataSourceTransactionManager=DEBUG`
- JPA 커밋 롤백 로그 확인
	- `logging.level.org.springframework.orm.jpa.JpaTransactionManager=DEBUG`
	- `logging.level.org.hibernate.resource.transaction=DEBUG`
- JPA SQL 로그 확인
	- `logging.level.org.hibernate.SQL=DEBUG`
	- `logging.level.org.hibernate.orm.jdbc.bind=TRACE`
- HTTP 요청 메시지 확인하기
	- `logging.level.org.apache.coyote.http11=trace`
