# Java 기본 특징

## 자바 표준 스펙
자바는 **표준 스펙**이 존재하고 여러 회사가 자신에 입맞에 맞게 이를 **구현**한다.
* 자바 표준 스펙
	* 자바의 설계도 문서
	* 자바 커뮤니티 프로세스(JCP)를 통해 관리
* 구현
	* 자바 표준 스펙에 맞춰 여러 회사가 각자에 최적화된 자바 프로그램을 개발
		* 오라클 Open JDK, Adoptium Eclipse Temurin, Amazon Corretto etc...
		* 오라클 Open JDK 사용하다가 Amazon Corretto 사용해도 대부분 큰 문제 없음
	* 각 회사들은 다양한 OS(Mac, Windows, 리눅스)에 맞는 자바도 함께 제공

## 컴파일과 실행
* 소스코드(Source code)
	* 개발자가 `.java` 확장자의 자바 소스코드를 작성한다.
* 컴파일(Compile) 단계
	* 자바가 제공하는 `javac` 를 사용해, `.java` -> `.class` 파일 생성
	* command: `javac Hello.java`
	* 즉, **자바 컴파일러가 소스코드를 바이트 코드로 변환**
	* 자바 가상 머신에서 더 빠르게 실행될 수 있게 최적화하고 syntax error 검출
* 실행(Runtime) 단계
	* `java` 프로그램을 사용해 자바를 띄우고 바이트코드인 `.class` 파일을 실행하면, JVM(실제 자바 프로그램 = 자바 가상 머신)이 띄워지면서 바이트코드를 읽고 프로그램을 실행한다.
	* command: `java Hello` (`Hello.class` 의 `.class` 확장자를 빼고 입력)

## 운영체제 독립성
* 일반적인 프로그램은 다른 운영체제에서 실행할 수 없다.
	* Windows 프로그램은 Windows OS가 사용하는 명령어들로 구성되어 있어서, 다른 OS와 호환되지 않음.
* 반면에, 자바 프로그램은 자바가 설치된 모든 OS에서 실행 가능 (**호환성**)
* 각 OS에 맞게 설치된 자바는 해당 OS의 명령어들로 컴파일된 `.class` 바이트코드를 실행
* 덕분에 *개발할 때*와 *서버 실행 시* 환경에 맞춰 *다른 자바를 사용*할 수 있다.
	* 개발: Mac, Windows
	* 서버: AWS Linux (Amazon Corretto 자바 설치)
___
## Reference
*[김영한의 자바 입문 - 코드로 시작하는 자바 첫걸음](https://www.inflearn.com/course/%EA%B9%80%EC%98%81%ED%95%9C%EC%9D%98-%EC%9E%90%EB%B0%94-%EC%9E%85%EB%AC%B8)*