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

## 유용한 명령어
- 의존관계 확인 (Tree view)
	- 프로젝트 디렉토리 - `./gradlew dependencies -configuration compileClasspath`
- 서버 재시작 없이 View 파일 변경하기
	- `spring-boot-devtools` 라이브러리 추가
	- html 파일만 컴파일 (`build` - `Recompile`)