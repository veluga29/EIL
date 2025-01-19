---
title: QueryDSL Dive
tags:
  - JPA
  - QueryDSL
date: 2025-01-13
thumbnail: ../../../assets/img/post_img/querydsl_img/querydsl_logo.png
---

## QueryDSL
- 소개
	- JPQL 빌더
	- 장점
		- 문자인 JPQL을 **코드**로 작성해 **컴파일 오류** 발생 가능
		- JPQL과 달리 **파라미터 바인딩을 자동 처리**
- 라이브러리 종류
	- `querydsl-apt`: Querydsl 관련 코드 생성 기능 제공 (Q 클래스 빌드)
	- `querydsl-jpa`: Querydsl 라이브러리

## 기본 문법
- **`JPAQueryFactory`**
	- 쿼리 작성의 기본 토대
	- `EntityManager`를 전달해 생성
		- **`JPAQueryFactory queryFactory = new JPAQueryFactory(em);`**
	- 필드에 두어도 **동시성 문제 걱정 없음**
		- `EntityManager`가 동시성 문제 걱정이 없기 때문에 마찬가지다
- **Q-Type**
	- 전략
		- **기본 인스턴스 사용** 방법을 **static import**해 사용하자
		- **같은 테이블을 조인**해야 하는 경우에만 **별칭 직접 지정** 방법을 사용하자
	- Q 클래스 인스턴스 사용 방법 2가지
		- 방법 1: 별칭 직접 지정
			- `QMember qMember = new QMember("m");`
			- 별칭 = JPQL 별칭
				- e.g. “select m from Member m” - m이 별칭
			- **같은 테이블을 조인해야할 때만 사용** (다른 때는 쓸 일 없음)
		- 방법 2: **기본 인스턴스 사용**
			- `QMember qMember = QMember.member;`
- `select`와 `from`
	- `select`, `from`
	- `selectFrom` (축약 버전)
- **검색 조건 쿼리** (`where`)
	- AND, OR 조건
		- `where` 조건에 **`,`로 파라미터를 추가**하면 **AND 조건** 형성
			- -> **null 값은 무시** -> **메서드 추출**을 활용해 **깔끔한 동적 쿼리 작성 가능**
			- e.g. `.where(member.username.eq("member1"), member.age.eq(10))`
		- `.and()`, `.or()`로 **메서드 체이닝** 가능
	- 검색 조건 예시
		```java
		member.username.eq("member1") // username = 'member1'
		member.username.ne("member1") //username != 'member1'
		member.username.eq("member1").not() // username != 'member1'
		member.username.isNotNull() //이름이 is not null
		member.age.in(10, 20) // age in (10,20)
		member.age.notIn(10, 20) // age not in (10, 20)
		member.age.between(10,30) //between 10, 30
		member.age.goe(30) // age >= 30
		member.age.gt(30) // age > 30
		member.age.loe(30) // age <= 30
		member.age.lt(30) // age < 30
		member.username.like("member%") //like 검색 
		member.username.contains("member") // like ‘%member%’ 검색
		member.username.startsWith("member") //like ‘member%’ 검색
		```
- **결과 조회**
	- **`fetch()`** : **리스트 조회**
		- 데이터 없으면 : 빈 리스트
	- **`fetchOne()`** : **단건 조회**
		- 결과가 없으면 : `null`
		- 결과가 둘 이상이면 : `com.querydsl.core.NonUniqueResultException` 
	- `fetchFirst()` : 처음 한 건 조회 (= `limit(1).fetchOne()`)
	- `fetchResults()` -> deprecated : 페이징 정보 포함 + total count 쿼리 추가 실행
	- `fetchCount()` -> deprecated : count 쿼리로 변경해서 count 수 조회
- 정렬 (`orderBy`)
	- `desc()`, `asc()` : 일반 정렬
	- `nullsLast()`, `nullsFirst()` : null 데이터 순서 부여
	- 사용 예시
		- `.orderBy(member.age.desc(), member.username.asc().nullsLast())`
		- 1번 순서: 회원 나이 내림차순(desc)
		- 2번 순서: 회원 이름 올림차순(asc)
			- 단, 2에서 회원 이름이 없으면 마지막에 출력(nulls last)
- 페이징 (`offset`, `limit`)
- 집합
	- 집합 함수
		```java
		List<Tuple> result = queryFactory
		        .select(member.count(),
		                member.age.sum(),
		                member.age.avg(),
		                member.age.max(),
			            member.age.min())
		        .from(member)
		        .fetch();
		
		Tuple tuple = result.get(0);
		tuple.get(member.count()); //회원수
		tuple.get(member.age.sum()); //나이 합
		tuple.get(member.age.avg()); //평균 나이
		tuple.get(member.age.max());
		tuple.get(member.age.min());
		```
	- `groupBy()`, `having()`
		```java
		.groupBy(item.price)
		.having(item.price.gt(1000))
		```
- **조인**
	- 기본 조인
		- **연관관계**로 조인
		- 문법: **`join(조인 대상, 별칭으로 사용할 Q타입)`**
			- e.g. 
				```java
				queryFactory
					.selectFrom(member)
					.join(member.team, team)
					.where(team.name.eq("teamA"))
					.fetch();
				```
		- 종류: `join()`, `innerJoin()`, `leftJoin()`, `rightJoin()`
	- 세타 조인
		- **연관관계가 없는 필드**로 조인
			- e.g. 
				```java
				queryFactory
					.select(member)
					.from(member, team)
					.where(member.username.eq(team.name))
					.fetch();
				```
		- 원리
			- **카타시안 조인**을 해버린 후 **where절로 필터링** (cross join 후 where 필터링)
			- **DB가 성능 최적화**함
		- 단점: **외부조인이 불가능**하므로 외부조인 **필요시 on 절을 사용**해야 함
	- `on` 절 활용 조인
		- 조인 대상 필터링
			- **외부조인**에 **필터링이 필요한 경우**에만 사용하자 (**내부 조인**이면 **`where` 절로 해결**)
				- 결과적으로 **left join에만 `on` 절 활용이 의미있는 결과**를 만듦
				- 내부조인(inner join)을 사용하면, `where` 절에서 필터링하는 것과 기능이 동일
			- e.g. 
				```java
				queryFactory
					.select(member, team)
					.from(member)
					.leftJoin(member.team, team)
					.on(team.name.eq("teamA"))
					.fetch();
				```
		- **연관관계 없는 엔터티 외부 조인** - 보통 이 이유로 많이 쓰임
			- 문법 차이: **leftJoin()** 부분에 일반 조인과 다르게 **엔티티 하나**만 들어감
				- 일반조인: `leftJoin(member.team, team)` - SQL on절에 id값 매칭 O
				- on조인: `from(member).leftJoin(team).on(xxx)` - SQL on절 id값 매칭 X
			- 참고) 내부조인도 가능
			- e.g.
				```java
				queryFactory
					.select(member, team)
					.from(member)
					.leftJoin(team)
					.on(member.username.eq(team.name))
					.fetch();
				```
- **페치 조인** (`fetchJoin()`)
	- `join()`, `leftJoin()` 등 **조인 기능 뒤에 `fetchJoin()` 추가**
	- e.g. 
		```java
		queryFactory
			.selectFrom(member)
			.join(member.team, team).fetchJoin()
			.where(member.username.eq("member1"))
			.fetchOne();
		```
- `distinct`
	- `select` 절 뒤에 **`distinct()` 추가** (JPQL distinct와 동일)
	- e.g.
		```java
		queryFactory
		    .select(member.username).distinct()
		    .from(member)
		    .fetch();
		```
- 서브 쿼리 (`JPAExpressions`) - **static import 활용**하면 코드가 더욱 깔끔해짐
	- 서브쿼리 지원
		- where 절 서브 쿼리 **지원**
		- select 절 서브 쿼리 **지원** (**하이버네이트** 사용 시 지원)
		- **from 절 서브 쿼리(인라인 뷰) 지원 X** (JPA, JPQL이 지원 X)
			- 해결책
				1. **서브 쿼리를 join으로 변경**하기 (높은 확률로 가능)
				2. 애플리케이션에서 **쿼리를 2번 분리해서 실행**하기
				3. nativeSQL을 사용하기
	- e.g. 
		```java
		queryFactory
			.selectFrom(member)
			.where(member.age.eq(
					JPAExpressions
				        .select(memberSub.age.max())
		                .from(memberSub)
		    ))
		    .fetch();
		```
- 기타
	- Case 문 (거의 사용 X)
		- select 절, 조건절(where), order by에서 사용 가능
		- e.g. 단순한 조건
			```java
			select(member.age
				   .when(10).then("열살")
				   .when(20).then("스무살")
				   .otherwise("기타"))
			```
		- e.g. 복잡한 조건
			```java
			select(new CaseBuilder()
				   .when(member.age.between(0, 20)).then("0~20살")
				   .when(member.age.between(21, 30)).then("21~30살")
				   .otherwise("기타"))
			```
		- e.g. 임의의 순서로 출력하기
			```java
			NumberExpression<Integer> rankPath = new CaseBuilder()
					.when(member.age.between(0, 20)).then(2)
					.when(member.age.between(21, 30)).then(1)
					.otherwise(3);
			
			List<Tuple> result = queryFactory
				.select(member.username, member.age, rankPath)
				.from(member)
				.orderBy(rankPath.desc())
				.fetch();
			```
	- 상수 (거의 사용 X)
		- `Expressions.constant(xxx)` 사용
		- e.g. `select(member.username, Expressions.constant("A"))`
	- 문자 더하기 (`concat`)
		- e.g. `select(member.username.concat("_").concat(member.age.stringValue()))`
		- 참고: 문자가 아닌 타입들은 **`stringValue()`** 로 **문자 변환 가능** (**ENUM 처리에도 자주 사용**)

>**복잡한 쿼리에 대한 제언**
>
>SQL이 화면을 맞추기위해 너무 복잡할 필요는 없다. (from… from… from…)
>따라서, **DB는 데이터를 퍼올리는 용도로만 사용하자**. (필터링, 그룹핑 등 **데이터를 최소화해 가져오는 역할**)
>그리고 뷰 로직은 애플리케이션의 프레젠테이션 계층에서 처리하자.
>
>결과적으로, **서브 쿼리와 복잡한 쿼리가 감소**할 것이다.

## 중급 문법
- **프로젝션** (select 대상 지정)
	- 프로젝션 대상이 **하나**
		- 타입을 명확하게 지정
		- e.g. `select(member.username)`
	- 프로젝션 대상이 **둘 이상**
		- **튜플** 조회 (`Tuple`)
			```java
			List<Tuple> result = queryFactory
			        .select(member.username, member.age)
			        .from(member)
			        .fetch();
			        
			for (Tuple tuple : result) {
				String username = tuple.get(member.username);
				Integer age = tuple.get(member.age);
			```
		- **DTO** 조회 (4가지 방법) => 실용적 관점에서는 `@QueryProjection`이 편리하나 답은 없음
			- 프로퍼티 접근 (Setter)
				- 이름(**별칭**)을 보고 매칭
				- e.g. `Projections.bean()`
					```java
					select(Projections.bean(MemberDto.class, 
						member.username,
						member.age)
					)
					```
			- 필드 직접 접근
				- getter, setter는 무시하고 **리플렉션** 등의 방법으로 **필드에 직접 값을 꽂음**
				- 이름(**별칭**)을 보고 매칭
				- e.g. `Projections.fields()`
					```java
					select(Projections.fields(MemberDto.class, 
						member.username, 
						member.age)
					)
					```
			- 생성자 사용
				- **타입**을 보고 매칭
				- e.g. `Projections.constructor()`
					```java
					select(Projections.constructor(MemberDto.class,
						member.username,
						member.age)
					)
					```
			- `@QueryProjection` (생성자 활용)
				- 사용법
					- DTO 설정
						```java
						@Data
						public class MemberDto {
						
						private String username;
						    private int age;
							
							public MemberDto() {}
							
							@QueryProjection
							public MemberDto(String username, int age) {
							    this.username = username;
							    this.age = age;
							}
						
						}
						```
						- **빌드** 후 DTO의 **Q 클래스 생성 확인**
					- 사용
						```java
						List<MemberDto> result = queryFactory
						    .select(new QMemberDto(member.username, member.age))
						    .from(member)
						    .fetch();
						```
				- 장점: **컴파일러 타입 체크**가 가능해 가장 **안전**
				- 단점
					- **DTO에 QueryDSL 애노테이션**을 유지 필요
					- DTO까지 **Q 파일을 생성**해야 함
			- 유의점: 프로퍼티 or 필드 직접 접근 방식에서 **이름이 다를 때**
				- **Q 클래스의 필드 이름**과 **DTO의 필드 이름**이 다르면 **별칭**으로 맞춰줘야 함
				- 별칭 적용 방법
					- `ExpressionUtils.as(source,alias)` : **필드**나 **서브 쿼리**에 별칭 적용
					- `username.as("memberName")` : **필드**에 별칭 적용
				- e.g.
					```java
					queryFactory
						.select(Projections.fields(UserDto.class,
						    member.username.as("name"),
							ExpressionUtils.as(
								JPAExpressions
								    .select(memberSub.age.max())
							        .from(memberSub), "age")
					        )
						).from(member)
						.fetch();
					```
- **동적 쿼리**
	- BooleanBuilder
		- 사용 예시
			```java
			private List<Member> searchMember1(String usernameCond, Integer ageCond) {
			    BooleanBuilder builder = new BooleanBuilder();
			    
			    if (usernameCond != null) {
			        builder.and(member.username.eq(usernameCond));
			    }
			    if (ageCond != null) {
			        builder.and(member.age.eq(ageCond));
			    }
			    
			    return queryFactory
					    .selectFrom(member)
					    .where(builder)
					    .fetch();
			}
			```
	- **Where 다중 파라미터 사용** (**권장**, **가장 깔끔**)
		- `where` 조건에 `null` 값은 무시
		- 검색조건의 반환결과는 `Predicate`보다 **`BooleanExpression`** 이 좋음 (**and, or 조립 가능**)
			- e.g. `private BooleanExpression usernameEq(String usernameCond)`
		- 장점
			- 메서드를 다른 쿼리에서도 **재활용** 가능
			- 쿼리 자체의 **가독성 상승**
			- **조합**을 사용하면 **반복적으로 쓰이는 코드를 묶어** 더 **직관적인 코드**로 **재사용** 가능
				- **`null` 체크는 조금 더 신경써야함** (e.g. `null.and(null)`)
				- e.g.1
					- 광고 상태를 나타내는 `isServiceable()` = `isValid()` + 날짜 `IN`
				- e.g.2
					```java
					private BooleanExpression allEq(String usernameCond, Integer ageCond) {
					    return usernameEq(usernameCond).and(ageEq(ageCond));
					}
					```
		- 사용 예시
			```java
			private List<Member> searchMember(String usernameCond, Integer ageCond) {
			    return queryFactory
			            .selectFrom(member)
			            .where(usernameEq(usernameCond), ageEq(ageCond))
			            .fetch();
			}
			
			private BooleanExpression usernameEq(String usernameCond) {
			    return usernameCond != null ? member.username.eq(usernameCond) : null;
			}
			
			private BooleanExpression ageEq(Integer ageCond) {
			    return ageCond != null ? member.age.eq(ageCond) : null;
			}
			```
- **수정 및 삭제 벌크 연산** (**`execute()`**)
	- 유의점: JPQL과 마찬가지로 **배치 쿼리 후**에는 **영속성 컨텍스트 초기화**가 안전 (`em.clear()`)
	- 대량 데이터 **수정**
		- 기본 수정
			```java
			long count = queryFactory
			         .update(member)
			         .set(member.username, "비회원")
			         .where(member.age.lt(28))
			         .execute();
			```
		- 기존 숫자에 1 더하기 (빼고 싶을 때는 -1 전달)
			```java
			long count = queryFactory
			         .update(member)
			         .set(member.age, member.age.add(1))
			         .execute();
			```
		- 곱하기: `.multiply(x)`
	- 대량 데이터 **삭제**
		```java
		long count = queryFactory
		         .delete(member)
			     .where(member.age.gt(18))
			     .execute();
		```
- SQL function 호출하기
	- JPA와 같이 Dialect에 등록된 내용만 호출 가능
		- e.g. "member"를 "M"으로 변경하는 `replace` 함수 사용
			```java
			String result = queryFactory
			        .select(Expressions.stringTemplate("function('replace', {0}, {1}, {2})", member.username, "member", "M"))
			        .from(member)
			        .fetchFirst();
			```
	- ANSI 표준 함수들은 QueryDSL이 상당 부분 내장
		- e.g. `lower()`
			- `.where(member.username.eq(member.username.lower()))`
