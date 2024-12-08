---
title: JPA 활용 팁 2
tags:
  - Java
  - ORM
  - JPA
date: 2024-11-26
---

## 요청과 응답 관련 유의 사항
- **요청 및 응답**은 **API 스펙**에 맞추어 **별도의 DTO로 전달하자** (엔터티 노출 X)
	- 엔터티를 요청과 응답에 사용하면 프레젠테이션 계층과 엔터티가 결합되어 오염됨 (`@NotEmpty` 등...)
		- e.g. `@RequestBody CreateMemberRequest request`
- **롬복**은 **DTO에 적극적으로 사용하자** (Entity에는 `getter` 정도 이외에는 사용 X)
- **CQS 개발 스타일** 적용하면 **유지보수성이 크게 향상**됨!
	- **Update 메서드**는 **반환없이 끝내거나 ID 값 정도만 반환**
		- Update가 엔터티 객체를 반환하면, 업데이트하면서 조회하는 꼴
	- Update 후 조회가 **필요**하다면, **PK로 하나 조회**하자
		- 특별히 트래픽 많은 API가 아니면 큰 이슈 X
		- e.g.
			- `memberService.update(id, request.getName());`
			- `Member findMember = memberService.findOne(id);`
- **API 응답**은 **처음부터 `Object`로 반환하자** (`Array` X)
	- 추후 Count를 넣어달라는 요청 등으로 언제든 요구사항이 변할 수 있음 (확장성을 위해)