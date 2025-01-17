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

## 쿼리 메서드
- 전략
	- **2개 정도 파라미터까지만 메서드 이름으로 쿼리 생성해 해결하자**
	- 더 길어지면 **`@Query`로 JPQL 직접 정의**해 풀자
- 스프링 데이터 JPA의 쿼리 메서드 탐색 전략
	1. `도메인 클래스 + .(점) + 메서드 이름`으로 Named Query를 찾음
		- `JpaRepository` 상속 시 제네릭으로 설정한 도메인 클래스
		- 인터페이스에 정의한 메서드 이름
	2. 없으면 메서드 이름으로 쿼리 생성
- 3가지 방법
	- **메서드 이름으로 쿼리 생성** (기본)
		- 규칙
			- `...`은 식별하기 위한 내용(설명)이므로 무엇이 들어가도 상관 없음
				- e.g. `findHelloBy` 처럼 `...`에 식별하기 위한 내용(설명)이 들어가도 됨
			- `By` 뒤에 원하는 속성과 조건을 입력하면 `where` 절로 간주
			- SQL에 들어갈 파라미터는 메서드의 파라미터로 받음 
		- 기본 제공 기능
			- 조회: `find...By`, `read...By`, `query...By`, `get...By`
			- COUNT: `count...By` 반환타입 `long`
			- EXISTS: `exists...By` 반환타입 `boolean`
			- 삭제: `delete...By`, `remove...By` 반환타입 `long` 
			- DISTINCT: `findDistinct`, `findMemberDistinctBy`
			- LIMIT: `findFirst3`, `findFirst`, `findTop`, `findTop3`
		- 장점
			- **엔터티 필드명을 변경**하면 메서드 이름도 변경해야 하는데, **컴파일 오류를 통해 인지 가능**
	- 메서드 이름으로 JPA NamedQuery 호출 (거의 사용 X)
		- 사용법
			- 엔터티에 정의된 JPA `@NamedQuery`의 `name`으로 메서드 이름 설정
		- 장점: 타입 안정성이 높음 (미리 정의된 정적 쿼리를 파싱을 통해 체크)
		- 단점: 엔터티에 쿼리가 있는 것도 좋지 않고, `@Query`가 훨씬 강력함
	- **`@Query` 적용** (**자주 사용**)
		- 인터페이스 메서드에 **JPQL 쿼리 직접 정의** 가능
		- 사용법
			- 하나의 값 조회
				```java
				@Query("select m.username from Member m")
				List<String> findUsernameList();
				```
			- DTO 직접 조회
				```java
				@Query("select new study.datajpa.dto.MemberDto(m.id, m.username, t.name) " +
				         "from Member m join m.team t")
				List<MemberDto> findMemberDto();
				```
			- 파라미터 바인딩
				```java
				@Query("select m from Member m where m.username = :name")
				Member findMembers(@Param("name") String username);
				```
				- **이름 기반 바인딩**을 하자 (위치 기반 바인딩 지양)
				- e.g. **`:name`** <-> **`@Param("name") `**
			- 컬렉션 파라미터 바인딩
				```java
				@Query("select m from Member m where m.username in :names")
				List<Member> findByNames(@Param("names") List<String> names);
				```
				- `Collection` 타입으로 in절 지원
		- 장점
			- **타입 안정성**이 높음
				- **정적 쿼리**라서 틀리면 애플리케이션 시작 시점에 **컴파일 에러**
				- 이름없는 Named 쿼리라 할 수 있다!
- 반환 타입
	- 스프링 데이터 JPA는 **반환 타입에 따라 `getSingleResult()` 혹은 `getResultList()` 등을 호출**
		```java
		List<Member> findByUsername(String name); //컬렉션 
		Member findByUsername(String name); //단건
		Optional<Member> findByUsername(String name); //단건 Optional
		```
		- **단건 조회** 결과가 있을지 없을지 모르겠다면 **Optional** 사용하자!!
	- 조회 결과가 많거나 없으면?
		- 컬렉션
			- 결과 없음: 빈 컬렉션 반환 
		- 단건 조회
			- 결과없음: `null` 반환
				- JPA는 `NoResultException` 발생, 스프링 데이터 JPA는 try~catch로 감싼 것
			- 결과가 2건 이상: `javax.persistence.NonUniqueResultException` 예외 발생
				- 결국엔 스프링 예외 `IncorrectResultSizeDataAccessException`로 변환됨


>**단건 조회 결과 Best Practice**
>
>자바 8 이전: **단건 조회의 결과가 없는 경우**, 예외가 나은지 null이 나은지는 논란
>=> 결론: 실무에서는 **null**이 낫다!
>
>**자바 8 이후** => DB에서 조회했는데 데이터가 있을지 없을지 모르면 **그냥 Optional을 써라!!!**