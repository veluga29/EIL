# 스프링 핵심 원리
## 스프링의 탄생
- EJB (Enterprise Java Beans) 지옥
	- 과거 EJB는 자바 진영의 표준이었다.
	- 분산 처리를 편리하게 도와주고 자체 ORM(Entity Bean)을 제공
	- 그러나 EJB 의존적 개발은 코드 관리가 심히 어려웠고 Entity Bean은 join이 잘 안될 정도로 기술력이 떨어짐
- 스프링의 탄생
	- Rod Johnson(로드 존슨)이 J2EE Design and Development 책 발행 (2002)
		- J2EE(=EJB)에 고통 받던 로드 존슨은 EJB 없이 고품질의 확장 가능한 애플리케이션 개발을 보여줌 (3만 줄 이상의 예제코드)
		- 현재의 스프링 핵심 개념 제시 (BeanFactory, ApplicationContext, POJO, IoC, DI etc...)
		- 개발자들은 열광했다.
	- Juergen Hoeller(유겐 휠러), Yann Caroff(얀 카로프)가 책이 아깝다며 오픈소스 프로젝트 제안
		- 스프링의 시작과 현재 (EJB의 겨울을 넘어 새로운 시작이라는 뜻)
		- 현재도 유겐 휠러가 스프링 핵심코드의 상당수를 개발 중
- JPA의 탄생
	- Gavin King(개빈 킹)이 EJB 엔터티 빈을 대체하는 Hibernate을 개발 (많은 개발자가 사용하게 됨)
	- 추후 자바 표준을 새로 만들 때, 개빈 킹을 영입해 JPA 표준을 만듦
		- 그 후 Hibernate이 다시 JPA 표준 구현체가 됨
		- 현재 JPA 구현체들 중 가장 많이 사용하는 것이 Hibernate이다.

## 스프링 생태계
- 필수
	- 스프링 프레임워크
		- 핵심 가치: **객체 지향 언어의 강점을 잘 살릴 수 있게 도와주는 프레임워크**
			- 핵심기술: 스프링 DI 컨테이너, AOP, 이벤트, 기타
			- 웹 기술: 스프링 MVC, 스프링 WebFlux
			- 데이터 접근 기술: 트랜잭션, JDBC, ORM 지원
			- 기술 통합: 캐시, 이메일, 원격접근, 스케줄링
			- 테스트: 스프링 기반 테스트 지원
			- 언어: 코틀린, 그루비
	- 스프링 부트
		- 스프링을 편리하게 사용할 수 있도록 지원
		- 쉬운 스프링 애플리케이션 생성
		- Tomcat 웹서버 설치 필요 없이 내장
		- 손쉬운 빌드 구성을 위한 starter 및 서드 파티 라이브러리 자동 구성
		- 모니터링 기능 제공
- 선택
	- 스프링 데이터
	- 스프링 세션
	- 스프링 시큐리티
	- 스프링 Rest Docs
	- 스프링 배치
	- 스프링 클라우드

## 객체 지향과 스프링
- 객체 지향의 핵심은 다형성이지만, 다형성만으로는 SOLID의 OCP, DIP 원칙을 지킬 수 없다.
	- `MemberRepository m = new MemoryMemberRepository();`
- **다형성 + OCP, DIP**를 지키려다보면 결국 **스프링 프레임워크**를 만들게 된다.
	- **의존관계 주입** (DI, Dependency Injection)
		- **런타임**에 외부에서 구현 객체를 생성하고 클라이언트에 전달해서 **실제 의존관계가 연결되는 것**
			- 스프링 이전: `AppConfig` 설정 클래스를 따로 만들고 구현 객체 생성, 연결 책임을 할당
				- 애플리케이션이 크게 **사용 영역**과 **구성 영역**으로 분리
					- **관심사의 분리**, **SRP** 준수
				- 구현 객체 변경시 사용 영역(클라이언트 코드) 변경 없이 **구성 영역만 영향** 받음
					- **다형성** + **DIP**가 잘 지켜지면 **OCP** 준수까지 이어짐
			- 스프링 이후:
				- 스프링은 **DI 컨테이너**(=**IoC 컨테이너**)를 통해 DI를 지원 (구성 영역)
				- 스프링 컨테이너 = `AppConfig` + `@Configuration` (+ `@Bean`)
		- **구성 영역** 덕분에 **클라이언트 코드 변경 없이 부품 갈아 끼우듯 런타임 기능 확장이 가능**
		- 정적인 클래스 의존관계를 변경하지 않고, **동적인 객체 의존관계를 쉽게 변경 가능**
	- **제어의 역전** (IoC, Inversion of Control)
		- **프로그램의 제어 흐름**을 직접 제어하는 것이 아니라 **외부에서 관리하는 것**
		- 구성영역(`AppConfig` 혹은 DI 컨테이너)이 프로그램의 제어흐름을 가져가면서 발생

>"역할에 따른 구현이 보인다"의 의미
>
>역할과 구현 클래스가 한눈에 들어오는 것을 말한다.
>**메소드 이름**, **리턴 타입**(**역할**), **리턴하는 객체**(**구현**)가 명확하게 보이는 코드가 좋다.

>프레임워크 VS 라이브러리
>
>**프레임워크**: 내가 작성하는 코드를 제어하고 대신 실행함 (`JUnit`)
>**라이브러리**: 내가 작성한 코드가 직접 제어의 흐름을 담당

>의존관계 분류
>
>**클래스 의존관계(정적):** 애플리케이션 실행 없이 `import` 코드만으로 파악하는 의존관계 (클래스 다이어그램)
>**객체 의존관계(동적)**: 애플리케이션 실행 시점(런타임)에 실제 생성되는 객체 인스턴스 간 의존관계 (객체 다이어그램)

## 스프링 컨테이너 (DI 컨테이너, IoC 컨테이너)
- **스프링 컨테이너** (`AppConfig` + `@Configuration` = `ApplicationContext`)
	- 스프링에서 의존관계 주입을 지원해주는 구성 영역
	- **`ApplicationContext`** 혹은 `BeanFactory`를 지칭
	- 구조
		1. `BeanFactory` (스프링 컨테이너 최상위 인터페이스)
			- 스프링 빈을 관리하고 조회하는 역할
			- `getBean()` 제공
		2. **`ApplicationContext`**(인터페이스, **주로 사용**) 
			![additional feature of application context](../image/additional_feature_of_application_context.png)
			- 빈 관리 및 조회 기능 (`BeanFactory` 상속 받음)
			- 부가 기능 제공
				- 국제화 기능, 환경변수 (로컬, 개발, 운영 구분), 애플리케이션 이벤트 (이벤트 발행 구독 모델 지원), 리소스 조회
		3. `ApplicationContext` **구현체** (다양한 형식의 설정 정보)
			![structure of spring container](../image/structure_of_spring_container.png)
			- 종류
				- **`AnnotationConfigApplicationContext`** (애노테이션 기반 자바 코드 설정)
				- `GenericXmlApplicationContext` (XML 설정)
				- `XxxApplicationContext`...
			- `BeanDefinition`
				![bean_definition_reader](../image/bean_definition_reader.png)
				- **빈 설정 메타정보**
				- `@Bean` 당 각각 하나씩 메타정보가 생성됨
				- **스프링 컨테이너는 `BeanDefinition` 인터페이스만 알고 해당 메타정보 기반으로 빈 생성**
				- 다양한 형식의 설정 정보는 실제로 `BeanDefinitionReader`가 읽고 `BeanDefinition`을 생성
					- `AnnotatedBeanDefinitionReader`
					- `XmlBeanDefinitionReader`
					- `XxxBeanDefinitionReader`…
- **스프링 빈**(`@Bean`)
	- 스프링 컨테이너에 등록된 객체
- 스프링 컨테이너 조회 메서드
	- 대원칙: **부모 타입을 조회하면, 자식 타입도 함께 조회한다.**
		- `Object`로 조회시 모든 스프링 빈 조회
	- 유의점
		- 구체 타입 조회(특정 하위 타입 조회 등)는 유연성이 감소되므로 지양
		- 개발시에는 굳이 컨테이너에 직접 빈을 조회할 일이 없음
	- 기본 조회
		- `ac.getBean(빈이름, 타입)`
		- `ac.getBean(타입)`
		- 예외
			- `NoSuchBeanDefinitionException: No bean named ...`
				- 조회 대상 빈이 없을 때
			- `NoUniqueBeanDefinitionException: No bean named ...`
				- 타입으로 조회시 같은 타입의 스프링 빈이 둘 이상일 때 (빈 이름 지정하면 해결)
	- 해당 타입의 모든 빈을 조회
		- `ac.getBeansOfType(타입)` 
	- 컨테이너에 등록된 모든 빈 이름 조회
		- `ac.getBeanDefinitionNames()`
	- Bean Definition 조회
		- `ac.getBeanDefinition(데피니션 네임)`
	- 빈 역할 조회
		- `beanDefinition.getRole()`
			- `ROLE_APPLICATION`: 일반적으로 사용자가 정의한 빈
			- `ROLE_INFRASTRUCTURE`: 스프링이 내부에서 사용하는 빈

>Class 내부의 static Class의 의미
>
>해당 클래스를 **현재 상위 클래스의 스코프 내에서만 사용**하겠다는 의미

>Spring Bean을 만드는 두 가지 일반적인 방법
>
>**직접 Spring Bean 등록 (=xml 방식)**
>BeanDefinition에 클래스 정보가 자세히 기록되어 있음
>`beanDefinition = Generic bean: class [hello.core.member.MemberServiceImpl]`
>
>**Factory method를 통해 등록** (=**Java config**를 통해 등록하는 방법)
>FactoryBean(=AppConfig) & Factory Method(=memberService 메서드)
>BeanDefinition에 `factoryBeanName=appConfig; factoryMethodName=memberService` 식으로 등록되어 있음

## **스프링 컨테이너 생성 과정**
1. 스프링 컨테이너 생성 단계
	- **구성 정보**(`AppConfig.class`)와 함께 컨테이너 객체 생성
	- `new AnnotationConfigApplicationContext(AppConfig.class)`
2. 스프링 빈 생성 및 등록 단계
	- 스프링 컨테이너는 설정 클래스 정보를 확인하면서 `@Bean`이 붙은 메서드를 **모두 호출**하고 메서드의 이름 Key, 메서드 반환 객체를 Value로 **스프링 빈 저장소에 등록**
		- 메서드 호출로 빈 객체 생성시 **의존관계 주입이 필요한 객체에 한해서 이 시점에 DI가 발생**
	- 빈 이름 = 메서드 명
		- 빈 이름 직접 부여 가능 - `@Bean(name="memberServiceNewNamed")`
		- **빈 이름은 항상 다른 이름을 부여해야 함** (다른 빈 무시 혹은 기존 빈 덮는 등의 오류)
3. 스프링 빈 의존관계 설정 단계
	- 스프링 컨테이너는 설정 정보를 참고해서 **의존관계 주입** (DI)

## 스프링 컨테이너 특징: 싱글톤 패턴
- 싱글톤 패턴: 클래스의 **인스턴스가 단 1개만 생성되는 것을 보장**하는 디자인 패턴
	```java
	public class SingletonService {
		//static 영역에 객체를 단 1개만 생성
		private static final SingletonService instance = new SingletonService();
		
		//객체 인스턴스가 필요하면 이 static 메서드를 통해서만 조회 허용
		public static SingletonService getInstance() {
		    return instance;
		}
	    
	    //private 생성자로 new 키워드를 사용한 객체 외부 생성 방지 
		private SingletonService() {
		}
	
		public void logic() { 
			System.out.println("싱글톤 객체 로직 호출");
		} 
	}
	```
	- 장점
		- 클라이언트 요청이 올 때마다 **이미 만들어진 객체를 공유해 메모리 낭비 없이 효율적으로 처리**할 수 있음
	- 단점
		- 구현 코드가 많고 **DIP, OCP 위반 가능성**을 높임
			- `memberService.getInstance()`로만 접근 가능하므로
		- **테스트하기 어렵고 유연성이 떨어짐**
			- 독립적인 단위테스트를 하려면 매번 공유된 인스턴스 초기화 필요
		- 안티패턴으로 불리기도 함
- 싱글톤 컨테이너
	- 스프링 컨테이너는 **싱글톤 패턴의 단점을 해결**하면서 **스프링 빈을 싱글톤으로 관리** (싱글톤 레지스트리)
		- Bean을 단 한 번만 생성하고 클라이언트 요청이 올 때마다 생성한 Bean을 공유
		- 싱글톤 패턴을 위한 지저분한 코드가 없어도 됨
		- DIP, OCP, 테스트, private 생성자로부터 자유롭게 싱글톤 사용 가능
- 주의점: 스프링 빈은 항상 **Stateless 설계**할 것
	- **싱글톤**은 여러 클라이언트가 하나의 객체 인스턴스를 공유하므로 **무상태**(**Stateless**)로 설계해야 함
	- 공유 필드가 큰 장애를 유발
	- 가급적 읽기에만 사용

>`@Configuration`과 **바이트 코드 조작 라이브러리**
>
>결론적으로 **항상 `@Configuration`을 사용해야 한다.**
>
>설정 정보에서 new를 사용해 여러 번 빈을 생성하는 자바코드를 볼 수 있다.
>그러나 실제로 빈은 한 번만 생성된다.
>이는 설정정보 클래스에 **`@Configuration`** 애노테이션이 붙어 있어 가능하다. **CGLIB**이라는 바이트코드 조작 라이브러리가 **설정 정보 클래스를 상속받아 임의의 다른 클래스를 만들고** 이를 스프링에 빈에 등록한다. 이 클래스는 **싱글톤 기능을 보장**해준다.
>
>`bean = class hello.core.AppConfig$$EnhancerBySpringCGLIB$$bd479d70`
>
>구체적으로 `@Bean`이 붙은 메서드마다 빈이 이미 존재하면 해당 빈을 반환하고, 없으면 생성해서 스프링 빈으로 등록하고 반환하는 코드가 동적으로 만들어진다.
>
>만일 `@Configuration`이 없다면, 매번 빈이 생성 및 등록되는 비효율적인 상황이 발생한다.
>즉, `@Bean`만 사용해도 스프링 빈으로 등록되지만, **싱글톤을 보장하지 않는다.**
>(이 경우 `@autowired`를 사용하여 의존관계 자동 주입으로 싱글톤 동작을 유도하는 방법이 있긴 하다.)

## 컴포넌트 스캔
- 설정 정보 없이 자동으로 스프링 빈을 등록하는 기능
- 애노테이션
	- `@ComponentScan`
		- 설정 정보 기반이 될 클래스에 애노테이션 추가 (`@Bean` 붙은 메서드 없어도 상관없음)
		- **`@Component`가 붙은 모든 클래스를 스프링 빈으로 등록**
	- `@Component`
		- **스프링 빈**으로 등록할 클래스에 애노테이션 추가
		- 네이밍 전략
			- 빈이름은 클래스명(첫글자만 소문자로)을 그대로 따라감
			- 직접 빈 이름 지정: `@Component("memberServiceNewName")`
	- `@Autowired`
		- 의존관계 자동 주입이 필요한 클래스에 애노테이션 추가
		- **스프링 컨테이너가 자동으로 해당 스프링 빈을 찾아서 주입**
		- 조회 전략
			- 기본은 타입이 같은 빈을 찾아 주입 - 같은 타입이 여러개 있으면 충돌
			- 생성자가 하나라면 애노테이션이 생략되어도 자동으로 조회
		- `@Autowired(required = false)`: 주입 대상이 없어도 동작
- 기본 스캔 대상
	- `@Component` 및 `@Component` 붙은 대상은 모두 스캔
	- `@Component`를 포함하고 있는 애노테이션
		- `@Controller`
			- MVC 컨트롤러로 인식
		- `@Service`
			- 특별한 처리 X, just 개발자의 비즈니스 계층 인식용
		- `@Repository`
			- 데이터 접근 계층 인식, 데이터 계층 예외를 스프링 예외로 변환
		- `@Configuration`
			- 스프링 설정 정보 인식 및 싱글톤 적용
	- `useDefaultFilters`이 기본적으로 켜져 있고, 옵션을 끄면 기본 스캔 대상들 제외도 가능
- 스캔 대상 필터 (`@ComponentScan` 파라미터)
	- `includeFilters`: 컴포넌트 스캔 대상을 추가로 지정
	- `excludeFilters`: 컴포넌트 스캔에서 제외할 대상 지정
- 빈 중복 등록으로 인한 스캔 충돌
	- 기본적으로 중복 등록을 하지 말자 (스프링 부트에서 에러 던짐)
	- 케이스
		- 자동 빈 등록 & 자동 빈 등록
			- `ConflictingBeanDefinitionException` 스프링 예외 발생
		- 수동 빈 등록 & 자동 빈 등록
			- 수동 빈 등록이 우선권 (수동 빈이 자동 빈을 오버라이딩)

## 의존관계 자동 주입
- 의존 관계 주입 방법
	- **생성자 주입** (**Recommandation** - Spring & Other DI Frameworks)
		- 생성자 호출 시점에 단 1번 호출되는 것이 보장
		- **불변**, **필수** 의존관계에 사용
			- 대부분의 의존관계 주입은 애플리케이션 종료까지 **변하면 안됨** (불변)
			- 생성자 주입 + `final`을 사용하면 필수적 주입 데이터 누락은 **컴파일 오류** (필수)
				- 다른 주입방법은 `final` 사용 불가
		- 실제 주요 사용 예
			- **생성자를 1개 두고 `@Autowired` 생략**
			- **lombok `@RequiredArgsConstructor` 적용**
				- `final` 필드를 모아 컴파일 시점에 생성자 코드 자동 생성
	- 수정자 주입 (Setter 주입, **필요 시 사용**) 
		- **선택**, **변경** 가능성이 있는 의존관계에 사용
	- 필드 주입 (**Don’t use**)
		- 외부 변경이 불가해 테스트가 힘듦
			- 예를 들어, 테스트에 실제 DB를 사용하지 않는 가짜 레포지토리 사용이 불가
			- 이를 위해 Setter를 만들면 세터 주입과 동일
			- 결국, DI 프레임워크 없이는 순수한 단위 테스트 불가
		- 테스트 코드, `@Configuration` 같은 특별한 용도에서만 사용
	- 일반 메서드 주입 (**Don’t use**)
		- 한 번에 여러 필드를 주입 받을 수 있다는 점이 수정자 주입과 차이점
- 옵셔널 처리 (주입할 스프링 빈이 없어도 동작해야 할 때)
	- `@Autowired(required=false)`
		- 자동 주입 대상이 없다면, 메서드 자체를 호출 x
	- `@Nullable`
		- 자동 주입 대상이 없다면, `null` 입력
		- 의존관계 파라미터의 타입
	- `Optional<>`
		- 자동 주입 대상이 없다면, `Optional.empty` 입력
		- 의존관계 파라미터의 타입
- 조회 빈이 2개 이상일 때
	- `@Autowired`는 타입으로 조회하므로 조회 빈이 여러 개일 때 충돌 발생
	- 해결책
		- **`@Primary`** (**Recommandation**)
			- 우선이 되는 빈에 적용
		- **`@Qualifier`** (**Optional**)
			- 추가 구분자(`@Qualifier(”임의의 구분자 이름”)`)를 빈과 실제 주입이 필요한 곳에 각각 적용
			- 우선권은 `@Primary`보다 높음
			- 실제 사용시에는 **커스텀 애노테이션**으로 만들면 **컴파일 시점에 타입 체크 가능**
		- `@Autowired` 필드 명 매칭 (**Don’t use**)
			- 빈이 여러 개일 때, 필드명 혹은 파라미터명으로 추가 매칭
	- 메인 데이터베이스 커넥션 획득 빈에 `@Primary`를 적용하고, 서브 데이터베이스 커넥션 획득 빈에 `@Quliafier`를 적용하는 케이스에 유용.
- 조회한 빈이 모두 필요할 때
	- `List`, `Map`으로 자동 의존관계 주입을 받으면 **모든 빈 조회 가능**
	- **전략 패턴** 구현 용이

>스프링 컨테이너 생성하며 스프링 빈 등록하기
>
>`new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class)`

## 자동 및 수동에 대한 실무 운영 기준
- 애플리케이션 = **업무 로직 빈** + **기술 지원 빈**
	- 업무 로직 빈
		- 컨트롤러, 서비스, 레포지토리 등 비즈니스 요구사항 개발 시 추가 및 변경되는 부분
	- 기술 지원 빈
		- 데이터베이스 연결, 로깅 등의 기술적인 문제나 AOP를 처리하는 부분
- 전략
	- **자동 기능**(컴포넌트 스캔, 의존관계 자동 주입)을 **기본으로 사용** (업무 로직)
	- **수동 빈** 등록 상황 (명시적이어서 유지 보수에 좋음)
		- 기술 지원 로직
		- 비즈니스 로직 중 다형성을 적극 활용할 때 (`DiscountPoilicy`)
			- **종류를 한 눈에 파악**하기 위해 수동 빈으로 등록 (**권장**)
			- 자동으로 하는 경우 **특정 패키지에 같이 묶어두기**


## 빈 생명주기 콜백
- DB 커넥션, 네트워크 소켓은 애플리케이션 시작 및 종료 시, 객체 초기화 & 종료 작업 필요
- 스프링 빈의 이벤트 라이프사이클
	1. 스프링 컨테이너 생성
	2. **스프링 빈 생성**
		- Constructor Injection이 일어날 수 있음
		- 생성자 주입은 자동 의존관계 주입이지만 **객체 생성이 어쩔 수 없이 되어야할 경우 스프링 빈 생성 시점에 의존관계 주입**이 일어남
	3. **의존관계 주입**
		- Setter or Field Injection
	4. **초기화 콜백**
		- 빈이 생성되고 의존관계 주입이 완료된 후 호출
	5. 사용
	6. **소멸전 콜백**
		- 빈이 소멸되기 직전에 호출
	7. 스프링 종료
- 방법
	- `@PostConstruct`, `@PreDestroy` (**Recommandation**)
		- 빈으로 등록될 실제 객체에 적용
		- 장점
			- **자바 표준**(**JSR-250**)이라 스프링이 아닌 다른 컨테이너에서도 동작
			- 스프링 권장 방법
		- 단점
			- **외부 라이브러리 적용 불가능**
	- 빈 등록 초기화, 종료 메서드 지정 (**외부 라이브러리 초기화 및 종료 시 사용**)
		- `@Bean(initMethod = "init", destroyMethod = "close")`
		- 빈으로 등록될 실제 객체에 `init`, `close` 메서드 만들고 지정
		- `destroyMethod` 속성은 지정하지 않아도 **종료 메서드를 자동 추론**해 호출
		- 장점
			- 메서드 이름 자유롭게 지정 가능
			- 스프링 코드에 의존 X
			- **외부 라이브러리 적용 가능**
	- 인터페이스 `InitializingBean`, `DisposableBean` (**Don’t use**)
		- `afterPropertiesSet`, `destroy` 메서드 오버라이딩으로 지원
		- 스프링 인터페이스라서 스프링에 의존하게 됨
		- 단점
			- 외부 라이브러리 적용 불가능

## 빈 스코프
- 빈이 존재할 수 있는 범위
- 애노테이션: **`@Scope(“지정 범위”)`** (자동 등록 및 수동 등록 모두 적용 가능)
- 종류
	- 싱글톤
		- 스프링 컨테이너의 시작과 종료까지 유지 (기본)
	- 프로토타입
		- 스프링 컨테이너에 요청할 때 마다 프로토타입 빈을 **생성**하고 **의존관계 주입** 및 **초기화 메서드 실행** 후 클라이언트에 반환
		- 이후 빈 관리 책임이 클라이언트에 있으므로 `@PreDestroy`같은 종료 메서드가 호출되지 않음
		- 필요시 클라이언트가 종료 메서드를 호출해야 함
	- 웹 관련 스코프 (웹 환경에서만 동작)
		- `request`
			- HTTP 요청 하나가 들어오고 나갈 때 까지 유지되는 스코프
			- **각각의 HTTP 요청마다** 별도의 빈 인스턴스가 생성되고 관리
		- `session`
			- HTTP Session과 동일한 생명주기를 가지는 스코프
		- `application`
			- 서블릿 컨텍스트와 동일한 생명주기를 가지는 스코프
		- `websocket`
			- 웹 소켓과 동일한 생명주기를 가지는 스코프
- 싱글톤 빈 + 프로토타입 빈 사용
	- **실무 문제는 대부분 싱글톤 빈으로 해결**할 수 있어서 프로토타입 빈을 직접 사용하는 경우가 드물다!
	- 원 시나리오
		- 싱글톤 빈과 프로토타입 빈을 함께 사용
		- 싱글톤 빈이 프로토타입 빈을 사용할 때마다 항상 새로운 프로토타입 빈 생성
	- 문제
		- 싱글톤 빈에 프로토타입 빈 의존관계 주입 시 프로토타입 빈이 미리 생성
		- 싱글톤 빈이 요청할 때마다 같은 프로토타입 빈을 반환
	- 해결책
		- **의존관계 조회**(**DL, Dependency Lookup**)
			- 외부에서 의존관계를 주입받지 않고, **컨테이너에서 직접 의존관계를 찾는 것**
			- 스프링 컨테이너로 직접 조회하므로 항상 새로운 프로토타입 빈 생성
		- 방법
			- **`Provider`**(**JSR-330**) (**Recommendation**)
				- `jakarta.inject.Provider`
				- `Provider`의 `get()`을 호출하면 DL 실행
				- 장점
					- **자바 표준** (다른 컨테이너에서도 사용 가능)
					- 기능이 단순해서 단위테스트 및 mock 코드 만들기 쉬움
				- 단점
					- 별도 라이브러리를 추가해야 함
					- `jakarta.inject:jakarta.inject-api:2.0.1`
			- `ObjectProvider`
				- `ObjetProvider`의 `getObject()`를 호출하면 내부에서 스프링 컨테이너를 통해 해당 빈을 찾아서 반환 (DL)
				- 단점
					- 스프링 의존
		- `Provider`, `ObjectProvider`는 프로토타입 뿐만 아니라 DL이 필요한 어떤 경우에도 사용 가능
- 싱글톤 빈 + 리퀘스트 스코프 빈
	- 원 시나리오
		- 컨트롤러 및 서비스 빈에 HTTP 요청마다 로거 객체를 DI
	- 문제
		- 컨트롤러 및 서비스는 싱글톤 빈이므로 스프링 앱 실행 시점에 생성 및 필요한 DI가 일어남
		- 그러나, 로거 객체는 request scope를 가져서 이 시점에 생성이 될 수 없어 에러 발생
	- 해결책 (**실제 빈 객체 조회를 필요한 시점까지 지연하기**)
		- **프록시 방식** (**Recommendation**)
			- `@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)`
				- 적용 대상이 클래스면 `TARGET_CLASS`
				- 적용 대상이 인터페이스면 `INTERFACES`
			- **가짜 프록시 클래스**를 만들어두고 HTTP request 상관없이 **미리 주입**해둘 수 있음
				- 가짜 프록시 객체는 request scope와 상관 없이 싱글톤처럼 동작
					- CGLIB 라이브러리로 내 클래스를 상속받은 가짜 프록시 객체 주입
				- 프록시 객체는 요청이 오면 내부에서 **진짜 빈을 요청하는 위임 로직** 실행
					- 프록시는 진짜 빈을 찾는 방법을 알고 있음
					- 클라이언트 메서드 호출 시 가짜 프록시 객체의 메서드를 호출한 것이고 프록시 객체는 request scope의 진짜 객체를 호출
					- 클라이언트는 원본인지 아닌지 모르게 동일하게 사용 (다형성)
		- Provider 방식
			- `Provider`, `ObjectProvider`

>표준과 프레임워크 기능 사이에서의 선택
>
>JPA의 경우 **JPA 자체가 표준이기에 표준을 사용**하면 된다.
>스프링의 경우 **스프링이 더 기능이 좋다 싶으면 스프링 기능을 사용하고**
>**유용성이 비슷하다 싶으면 자바 표준을 사용하면 좋을 것 같다.** 
>(자바 표준이 있어도 사실상 스프링 이외의 컨테이너를 쓸 일이 없기 때문)
>
>흔치 않지만, 스프링이 아닌 **다른 컨테이너에서도 사용할 수 있어야 하면 표준을 사용해야 한다.**

>`spring-boot-starter-web`
>
>웹 라이브러리가 없으면 스프링 부트는 `AnnotationConfigApplicationContext` 기반으로 애플리케이션을 구동한다.
>반면에, 웹 라이브러리가 있으면 `AnnotationConfigServletWebServerApplicationContext`를 기반으로 구동한다. (웹 관련 추가 설정 및 환경이 필요하므로)

***
## Reference
*[스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)*