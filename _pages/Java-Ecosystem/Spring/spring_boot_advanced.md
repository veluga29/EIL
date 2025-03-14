---
title: 스프링 부트 핵심 원리와 활용
tags:
  - Java
  - Spring
  - SpringBoot
date: 2025-03-11
thumbnail: ../../../assets/img/post_img/spring_boot_img/spring_boot_advanced_logo.png
---

## 스프링 부트의 필요성
- 스프링은 2013년까지 크게 성장해왔지만, 프로젝트 시작 시 필요한 설정이 점점 늘어나 어려워짐
- 스프링 부트 (2014~)
	- **스프링을 편리하게 사용할 수 있도록 지원하는 도구**
	- 스프링 부트가 **프로젝트 시작을 위한 복잡한 설정 과정을 해결** -> 개발 시간 단축
- 핵심 기능
	- **WAS**
		- Tomcat 같은 웹서버를 내장 (별도 웹 서버 설치 필요 X)
	- **라이브러리 관리**
		- 손쉬운 스타터 종속성 제공, 스프링과 외부 라이브러리의 버전 호환을 자동 관리
	- **자동 구성**
		- 프로젝트 시작에 필요한 스프링과 외부 라이브러리의 빈을 자동 등록
	- **외부 설정 공통화**
	- **프로덕션 준비**
		- 모니터링을 위한 메트릭, 상태 확인 기능 제공

## 스프링 부트가 제공하는 라이브러리 관리 기능
- **외부 라이브러리 버전 관리**
	- 개발자는 원하는 라이브러리만 고르고 **버전은 생략**
	- 스프링 부트가 **부트 버전에 맞춘 최적화된 라이브러리 버전을 선택해줌** (**호환성** 테스트 완료)
		```java
		plugins {
			id 'org.springframework.boot' version '3.0.2'
			id 'io.spring.dependency-management' version '1.1.0' //추가
			id 'java'
		}
		```
		- **`dependency-management` 플러그인** 사용
			- `spring-boot-dependencies`의 bom 정보를 참고
			- **bom**(Bill of Materials, 부품 목록)
				- 현재 프로젝트에 지정한 스프링 부트 버전에 맞는 라이브러리 버전 명시
		- `spring-boot-dependencies`는 gradle 플러그인에서 사용하므로 눈에 보이진 않음
- **스프링 부트 스타터** 제공
	- **Best Practice 라이브러리 뭉치를 제공**
		- 덕분에 일반적으로 많이 사용하는 대중적인 라이브러리로 **간단하게 프로젝트 시작 가능**
	- 이름 패턴
		- 공식: `spring-boot-starter-*`
		- 비공식: `thirdpartyproject-spring-boot-starter` (스프링이 공식적 제공 X)
			- e.g. `mybatis-spring-boot-starter`
	- 자주 사용하는 스타터
		- `spring-boot-starter` : 핵심 스타터, 자동 구성, 로깅, YAML (다른 스타터 사용 시 보통 포함됨)
		- `spring-boot-starter-jdbc` : JDBC, HikariCP 커넥션풀
		- `spring-boot-starter-data-jpa` : 스프링 데이터 JPA, 하이버네이트
		- `spring-boot-starter-data-mongodb` : 스프링 데이터 몽고
		- `spring-boot-starter-data-redis` : 스프링 데이터 Redis, Lettuce 클라이언트
		- `spring-boot-starter-thymeleaf` : 타임리프 뷰와 웹 MVC
		- `spring-boot-starter-web` : 웹 구축을 위한 스타터, RESTful, 스프링 MVC, 내장 톰캣
		- `spring-boot-starter-validation` : 자바 빈 검증기(하이버네이트 Validator)
		- `spring-boot-starter-batch` : 스프링 배치를 위한 스타터

>참고: 스프링 부트가 관리하지 않는 **외부 라이브러리 사용하기**
>- 버전 없이 적용해보고 안되면 버전 명시
>- e.g. `implementation 'org.yaml:snakeyaml:1.30'`

>참고: 스프링 부트가 관리하는 **라이브러리의 버전 변경** 방법
>- e.g. `ext['tomcat.version'] = '10.1.4'`
>- 거의 변경할 일이 없지만, 혹시나 버그 때문에 버전을 바꿔야 한다면 사용
>- `tomcat.version` 같은 속성값은 스프링 부트 docs에서 확인하자