# 스프링 시작하기
## 빌드 및 실행 방법
- `./gradlew build`
- `cd build/libs`
- `java -jar hello-spring-0.0.1-SNAPSHOT.jar`
	- 빌드가 잘 안될 때는 `./gradlew clean build` 후 다시 빌드 (빌드 폴더 삭제)

## 주요 라이브러리 의존관계
- **스프링 부트 라이브러리**
	- spring-boot-starter-web
		- spring-boot-starter-tomcat: 톰캣 (웹서버)
		- spring-webmvc: 스프링 웹 MVC
	- spring-boot-starter-thymeleaf: 타임리프 템플릿 엔진(View) 
	- spring-boot-starter(공통): 스프링 부트 + 스프링 코어 + 로깅
		- spring-boot
			- spring-core
		- spring-boot-starter-logging
			- logback, slf4j
- **테스트 라이브러리**
	- spring-boot-starter-test
		- junit: 테스트 프레임워크  
		- mockito: 목 라이브러리  
		- assertj: 테스트 코드를 좀 더 편하게 작성하게 도와주는 라이브러리 
		- spring-test: 스프링 통합 테스트 지원

## 웹 개발 유형 변화
- **정적 컨텐츠**
	- 서버가 HTML 파일을 브라우저에게 그대로 넘겨주는 방식
	- 과거에는 뷰와 컨트롤러 분리 없이 뷰로 모든 것을 다 하는 모델 원 방식을 사용
	- `static/index.html`
		- 해당 경로로 `index.html`을 생성해두면 Welcome page가 된다.
	- 예시
		- 요청: `localhost:8080/hello-static.html`
		- 톰켓 서버가 스프링 컨테이너에 요청 전달
		- 요청에 대한 컨트롤러가 없다면, `resources/static/hello-static.html`을 찾아 브라우저에 반환
- **MVC와 템플릿 엔진**
	- 컨트롤러에서 리턴 값으로 문자를 반환하면 뷰 리졸버(`viewResolver`)가 화면을 찾아 처리한다.
		- 템플릿 엔진 viewName 매핑
		- `resources/templates` 하위에 있는 `{viewName}.html` 파일을 찾아 템플릿 엔진 처리 후 브라우저에 반환
	- 예시
		- 요청: `localhost:8080/hello-mvc`
		- 톰켓 서버가 스프링 컨테이너에 요청 전달
		- 요청에 대한 컨트롤러를 찾음
		- 컨트롤러는 `hello-template`이라는 문자열을 반환
		- `viewResolver`는 viewName 매핑으로 `resources/templates/hello-template.html`을 찾음
		- `viewResolver`가 Thymeleaf 템플릿 엔진에게 처리를 넘김
		- 템플릿 엔진이 렌더링해서 변환한 HTML을 브라우저에게 반환
- **API**
	- @ResponseBody를 붙인 컨트롤러는 viewResolver를 사용하지 않는다.
	- 결과 값을 JSON 형태로 HTTP Body에 담아 반환한다.
	- `viewResolver` 대신 `HttpMessageConverter`가 동작
		- HTTP Accept 헤더와 컨트롤러 반환 타입 정보를 조합해 `HttpMessageConverter`가 선택됨
			- `StringHttpMessageConverter` (기본 문자처리)
			- `MappingJackson2HttpMessageConverter` (기본 객체처리)
			- `xmlHttpMessageConverter` (accept header에 xml로 요청)
	- 예시
		- 요청: `localhost:8080/hello-api`
		- 톰켓 서버가 스프링 컨테이너에 요청 전달
		- 요청에 대한 컨트롤러를 찾음
		- `@ResponseBody`가 붙은 컨트롤러이므로 반환값이 `HttpMessageConverter`에 전달됨
		- `HttpMessageConverter`가 데이터를 직렬화 후 HTTP Body에 담아 반환

## Spring Bean
스프링 빈은 각 객체들 간의 의존관계를 저장하여 사용하기 편리하게 지원한다.
- 의존관계 등록방법
	- **자동 의존관계 설정 (Component Scan)**
		- 정형화된 컨트롤러, 서비스, 레포지토리 같은 코드에 컴포넌트 스캔을 사용한다.
		- 컴포넌트 등록
			- `@Component` 애노테이션이 있으면 스프링 빈으로 자동 등록된다.
			- 다음 어노테이션들은 @Component를 포함하고 있어 역시 자동 등록된다.
				- `@Controller`
				- `@Service`
				- `@Repository`
		- `@Autowired`
			- 해당 어노테이션이 붙은 메소드에게 스프링이 자동으로 연관 객체를 DI(의존성 주입)한다.
			- 생성자에 사용시 객체 생성시점에 의존성 주입한다.
				- 생성자가 하나라면 `@Autowired` 생략 가능
	- **수동 의존관계 설정 (SpringConfig.java)**
		- 정형화되지 않거나 상황에 따라 구현 클래스를 변경해야 한다면 직접 설정으로 등록한다.
			- 레포지토리를 다양하게 변경해야 하는 상황이라면 관리 포인트가 하나가 되어 편리
		- 등록 방법
			- `@Service`, `@Repository`, `@Autowired` 등 현 상황에 불필요한 어노테이션은 제거
			- 현재 앱 디렉토리에 `SpringConfig.java` 클래스 생성
			- 클래스에 `@Configuration`, 메서드에 `@Bean` 어노테이션을 설정
			- 메서드마다 필요한 인스턴스를 반환
				```java
				@Configuration
				public class SpringConfig {
				
					@Bean
					public MemberService memberService() {
					    return new MemberService(memberRepository());
					}
				
				    @Bean     
				    public MemberRepository memberRepository() {
					    return new MemoryMemberRepository();
				    }
				}
				```
- 특징
	- 스프링은 스프링 빈에 등록할 때, 객체를 싱글톤으로 등록한다.
	- 즉, 같은 스프링 빈이면 모두 같은 인스턴스여서 메모리가 절약된다. (설정으로 변경은 가능)
	- 예시
		- 주문 컨트롤러에서 멤버 서비스, 멤버 레포지토리를 요청하면 똑같은 인스턴스를 넣어 줌

## 스프링 통합 테스트
테스트 클래스에 다음 어노테이션을 추가한다.
- `@SpringBootTest`
	- 스프링 컨테이너와 테스트를 함께 실행한다.
- `@Transactional`
	- 테스트 케이스에 해당 어노테이션을 추가하면, 테스트 시작 전에 트랜잭션을 시작하고 테스트 완료 후 항상 롤백한다. 덕분에 DB에 데이터가 남지 않아 각각의 테스트가 서로 영향을 주지 않는다.

## 스프링 DB 접근 기술
- 순수 JDBC
	- 관련 코드들이 매우 장황하고 반복이 많다.
- JdbcTemplate
	- SQL은 여전히 직접 작성하지만, JDBC의 반복 코드를 대부분 제거해준다.
- JPA
	- ORM의 개념으로 넘어왔다. 데이터 중심 설계에서 객체 중심 설계 패러다임으로 전환할 수 있고 생산성을 크게 높인다.
	- jdbc 관련 라이브러리가 포함되어 있다.
	- 항상 `@Transactional`을 사용해서 데이터 변경을 트랜잭션 안에서 실행 시켜야 한다.
- 스프링 데이터 JPA
	- 인터페이스만으로 개발 가능
	- 메서드 이름만으로 조회 기능 제공
	- 페이징 기능 자동 제공
	- 단순 반복 코드가 크게 줄어드는 덕분에 개발자는 비즈니스 로직에 집중할 수 있다.
- Querydsl
	- 복잡한 동적 쿼리 작업

## AOP
- Aspect of Programming
	- **관점 지향 프로그래밍**
	- 어떤 로직을 핵심 관심 사항, 공통 관심 사항으로 나누고 그 관점을 바탕으로 각각 모듈화
		- 핵심 관심 사항 (core concern)
			- 비즈니스 로직
		- 공통 관심 사항 (cross-cutting concern)
			- DB 연결, 로깅, 파일 입출력, API 계층별 응답 시간 측정 etc...
	- `@Aspect`, `@Around`
- AOP in Spring
	- 스프링은 프록시 방식의 AOP를 사용
	- 스프링은 AOP가 있으면 서버가 올라올 때 컨테이너에서 스프링 빈에 등록하면서 **가짜 스프링 빈을 앞에 세우고 그것이 끝나면 진짜 스프링 빈을 호출**하도록 동작한다. (가짜 서비스 후 진짜 서비스 호출)
	- 컨트롤러에서 서비스를 호출하면 서비스 코드는 스프링 빈을 통해 가짜 프록시 서비스를 의존성 주입 받고 해당 프록시 서비스가 끝나면 다시 실제 서비스 코드가 의존성 주입을 받아 실행된다.
	- 이 방식은 **스프링이 DI가 가능하기 떄문에 할 수 있는 기술**