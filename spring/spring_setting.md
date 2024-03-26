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
- `ddl-auto: create`
	- 애플리케이션 실행 시점에 데이블을 drop하고 다시 생성
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