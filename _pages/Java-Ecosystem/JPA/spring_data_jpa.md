---
title: Spring Data JPA Dive
tags:
  - JPA
  - Spring-Data-JPA
date: 2025-01-09
thumbnail: ../../../assets/img/post_img/spring_data_jpa_img/spring_data_jpa_logo.png
---
## 스프링 데이터와 스프링 데이터 JPA
- 스프링 데이터 프로젝트
	- **기본 데이터 저장소의 특수성을 유지**하면서 **익숙하고 일관된 Spring 기반 데이터 액세스** 제공을 목표
	- 스프링 데이터 몽고, 스프링 데이터 레디스, 스프링 데이터 JPA 등이 포함
- 패키지 구조
	- `spring-data-commons` 패키지: Spring-Data 프로젝트(몽고, 레디스, JPA) **모두가 공유**
		- e.g. 
			- `Repository`(마커 인터페이스)
			- `CrudRepository`, `PagingAndSortingRepository`
	- `spring-data-jpa` 패키지: **JPA**를 위한 스프링 데이터 저장소 지원
		- e.g. `JpaRepository` 인터페이스, `SimpleJpaRepository` 클래스
- 장점: **유사한 인터페이스로 편하게 개발 가능**
	- DB 변경은 큰 작업이라 거의 일어나지 않으므로, 구현체 교체의 편리함은 장점이 아님

## 공통 인터페이스
- 기본 사용법
	- 임의의 설정 클래스에 `@EnableJpaRepositories` 적용 - **스프링 부트 사용시 생략 가능**
		```java
		@Configuration
		@EnableJpaRepositories(basePackages = "jpabook.jpashop.repository")
		public class AppConfig {}
		```
		- 만약 적용하고자 하는 패키지가 다르다면, `@EnableJpaRepositories`를 적용하자
	- **`JpaRepository`(혹은 부모 인터페이스)를 상속한 인터페이스 만들기**
		- **`@Repository`도 생략 가능** - **스프링 데이터 JPA**가 같은 기능 **자동 처리**
			- 컴포넌트 스캔 처리
			- JPA 예외 -> 스프링 예외 변환 처리
- 기본 원리
	![basic_flow](../../../assets/img/post_img/spring_data_jpa_img/basic_flow.png)
	- 애플리케이션 로딩 시 **클래스 스캔** 진행
	- `JpaRepository`~`Repository`를 **상속한 인터페이스를 모두 찾음**
		- `org.springframework.data.repository.Repository`를 상속한 인터페이스를 찾음
	- **스프링 데이터 JPA가 구현 클래스 생성** (프록시 구현체)
	- 이후 필요한 곳에 주입
- `JpaRepository` 인터페이스
	- 대부분의 공통 CRUD 제공 
	- 제네릭은 `<엔티티 타입, 식별자 타입(PK)>` 설정
	- 주요 메서드 (상속한 인터페이스 포함)
		- `save(S)` : **새로운 엔티티는 저장**하고 **이미 있는 엔티티는 병합**
		- `delete(T)` : 엔티티 하나를 삭제 (내부에서 `EntityManager.remove()` 호출)
		- `findById(ID)` : 엔티티 하나를 조회 (내부에서 `EntityManager.find()` 호출)
		- `getOne(ID)` : 엔티티를 프록시로 조회 (내부에서 `EntityManager.getReference()` 호출)
		- `findAll(...)` : 모든 엔티티를 조회
			- 정렬 및 페이징 조건을 파라미터로 제공 (`Sort`, `Pageable`)
		- `existsById(ID)`
