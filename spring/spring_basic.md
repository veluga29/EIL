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
	- 스프링은 DI 컨테이너를 통해 DI를 지원
	- 덕분에 클라이언트 코드 변경 없이 부품 갈아 끼우듯 런타임 기능 확장이 가능


***
## Reference
*[스프링 핵심 원리 - 기본편](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%95%B5%EC%8B%AC-%EC%9B%90%EB%A6%AC-%EA%B8%B0%EB%B3%B8%ED%8E%B8)*