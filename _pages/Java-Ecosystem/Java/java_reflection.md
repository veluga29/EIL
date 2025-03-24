---
title: 자바 리플렉션
tags:
  - Java
date: 2025-03-15
thumbnail: ../../../assets/img/post_img/java_img/java_io_network_logo.png
---

## 리플렉션
- 클래스가 제공하는 다양한 정보(**메타 데이터**)를 **런타임에 동적으로 분석하고 사용**하는 기능
- 메타데이터 종류
	- 클래스
		- e.g. 클래스 이름, 접근 제어자, 부모 클래스, 구현한 인터페이스
	- 필드
		- e.g. 필드 이름, 타입, 접근 제어자
		- **해당 필드 값을 읽거나 수정 가능**
	- 메서드
		- e.g. 메서드 이름, 반환 타입, 매개변수 정보
		- **런타임에 동적으로 메서드 호출 가능**
	- 생성자
		- e.g. 매개변수 타입 및 개수
		- **런타임에 동적으로 객체 생성 가능**

## 클래스 메타데이터
- 클래스의 메타데이터는 **`Class` 클래스**로 표현
- `Class` 조회 방법
	- **클래스에서 찾기**
		- **`클래스명.class`**
		- e.g. `Class<BasicData> basicDataClass1 = BasicData.class;`
	- **인스턴스에서 찾기**
		- **`인스턴스.getClass()`**
		- e.g.
			- `BasicData basicInstance = new BasicData();`
			- `Class<? extends BasicData> basicDataClass2 = basicInstance.getClass();`
	- **문자로 찾기**
		- **`Class.forName(패키지명문자열)`**
		- e.g.
			- `String className = "reflection.data.BasicData";`
			- `Class<?> basicDataClass3 = Class.forName(className);`
- 기본 정보 탐색
	- 클래스 이름
		- 경로 포함 이름: `basicData.getName() //reflection.data.BasicData`
		- 클래스 이름: `basicData.getSimpleName() //BasicData`
	- 패키지
		- `basicData.getPackage() //package reflection.data`
	- 부모 클래스
		- `basicData.getSuperclass() //class java.lang.Object`
	- 구현한 인터페이스
		- `basicData.getInterfaces() //[]`
	- 조건 판별
		- `basicData.isInterface() //false`
		- `basicData.isEnum() //false`
		- `basicData.isAnnotation() //false`
	- 수정자 정보 (규칙있는 숫자로 리턴)
		- `basicData.getModifiers() //1`
		- 참고: 수정자는 접근제어자와 비접근제어자(기타 수정자)로 분류
			- 접근 제어자: `public` , `protected` , `default` ( `package-private` ), `private`
			- 비 접근 제어자: `static` , `final` , `abstract` , `synchronized` , `volatile` 등

