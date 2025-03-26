---
title: 자바 애노테이션
tags:
  - Java
date: 2025-03-25
thumbnail: ../../../assets/img/post_img/java_img/java_io_network_logo.png
---
## 애노테이션 (Annotation)
- **프로그램 실행 중에 읽어서 사용할 수 있는 특별한 주석**
	- 내부에서 리플렉션 같은 기술 등을 활용
		- e.g. `Class` ,`Method` ,`Field` ,`Constructor` 클래스는 `getAnnotation()` 제공
	- 참고: 본래 주석은 코드가 아니므로 컴파일 시점에 모두 제거됨
- 코드에 메모를 달아 놓는 것처럼 **코드에 대한 메타데이터**를 표현
	- 프로그램 코드가 아니어서 애노테이션이 달린 메서드를 호출해도 영향을 주지 않음

## 애노테이션 정의 규칙
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
public @interface AnnoElement {
	String value();
	int count() default 0;
	String[] tags() default {};
	
	Class<? extends MyLogger> annoData() default MyLogger.class;
	//MyLogger data(); // 다른 타입은 적용X
}
```
- **정의**: `@interface` 키워드
- **속성**: 애노테이션은 속성을 가질 수 있음
	- 요소 이름
		- **메서드 형태**로 정의
		- **괄호()를 포함**하되 **매개변수는 없어야 함**
	- 데이터 타입
		- 기본 타입 (`int`, `float`, `boolean` 등)
		- `String`
		- `Class` (메타데이터) 또는 인터페이스 (직접 정의한 것이 아닌 Class에 대한 정보)
		- `enum`
		- 다른 애노테이션 타입
		- 위의 타입들의 배열
		- 앞서 설명한 타입 외에는 정의 불가 (즉, 일반적인 클래스를 사용할 수 없음)
			- 예) `Member` , `User` , `MyLogger`
	- `default` 값
		- 요소에 default 값을 지정 가능
		- 예: `String value() default "기본 값을 적용합니다.";`
	- 반환 값
		- `void` 반환 타입 사용 불가
	- 예외
		- 예외 선언 불가
- 메타 애노테이션: **애노테이션을 정의하는데 사용하는 특별한 애노테이션**
	- **`@Retention`**
		- 애노테이션의 **생존 기간**을 지정
		- 종류: `RetentionPolicy` enum
			- `RetentionPolicy.SOURCE` (특별한 경우 사용)
				- 소스코드에서만 생존 -> 컴파일 시점에 제거
			- `RetentionPolicy.CLASS` (기본값, 그러나 거의 사용 X)
				- 컴파일 후 `.class` 파일까지 생존 -> 자바 실행 시점에 제거
			- **`RetentionPolicy.RUNTIME`** (**대부분 사용**)
				- 자바 실행 중에도 생존
				- **런타임에 리플렉션으로 읽을 수 있어 자주 사용됨**
	- **`@Target`**
		- 애노테이션을 **적용할 수 있는 위치** 지정
			- 지정하지 않은 곳에 애노테이션을 적용하면 **컴파일 오류** 발생
		- 종류: `ElementType` enum
			- **주로 `TYPE`, `FIELD`, `METHOD` 사용**
		- e.g. 배열로 여러 위치도 적용 가능
			- `@Target({ElementType.METHOD, ElementType.TYPE})`
	- **`@Documented`** (**보통 함께 사용**)
		- **자바 API 문서**를 만들 때, 해당 애노테이션이 문서에 포함되어 표현되도록 지정
	- `@Inherited`
		- 자식 클래스가 애노테이션을 상속 받을 수 있게 함

## 애노테이션 사용법
- 기본
	```java
	@AnnoElement(value = "data", count = 10, tags = {"t1", "t2"})
	public class ElementData1 {
	}
	```
- 배열 항목이 하나인 경우 `{}` 생략 가능 & default 항목은 생략 가능
	```java
	@AnnoElement(value = "data", tags = "t1")
	public class ElementData2 {
	}
	```
- **입력 요소가 하나인 경우 `value` 키 생략 가능** (`value = "data"` 와 동일)
	```java
	 @AnnoElement("data")
	public class ElementData3 {
	}
	```

## 애노테이션과 상속
```java
public interface Annotation {
    boolean equals(Object obj);
    int hashCode();
    String toString();
    Class<? extends Annotation> annotationType(); //애노테이션 타입 반환
}
```
- **자바**에서 애노테이션은 **특별한 형태의 인터페이스로 간주**
- **모든 애노테이션**은 **`java.lang.annotation.Annotation` 인터페이스**를 **묵시적으로 상속** 받음
	- **`@interface`로 정의**하면 **자바 컴파일러가 자동으로 `Annotation` 인터페이스를 확장**
		- 애노테이션 정의
			```java
			public @interface MyCustomAnnotation {}
			```
		- 자바가 자동으로 처리
			```java
			public interface MyCustomAnnotation extends java.lang.annotation.Annotation {}
			```
- 다만, **애노테이션 사이**에는 상속이라는 개념이 존재 X
	- 애노테이션은 오직 `Annotation` 인터페이스만 상속
	- 애노테이션은 **다른 애노테이션이나 인터페이스를 직접 상속할 수 없음**
- `@Inherited`
	- 애노테이션을 적용한 클래스의 **자식 클래스**도 **해당 애노테이션을 부여** 받을 수 있음
	- **클래스 상속**에서만 작동 (인터페이스 구현에는 적용 X)

## 자바 기본 애노테이션
- `@Override`
	- 메서드 재정의가 정확하게 잘 되었는지 컴파일러가 체크하는데 사용
- `@Deprecated`
	- 더 이상 사용되지 않는다는 뜻을 표현하며, 적용된 기능은 사용을 권장하지 않음
	- 컴파일 시점에 경고를 나타내지만, 프로그램은 작동
	- 옵션
		- `since` : 더 이상 사용하지 않게된 버전 정보 
		- `forRemoval` : 미래 버전에 코드가 제거될 예정 (**더더욱 강력한 경고**)
	- 예제
		- `@Deprecated` -> 진짜 쓰지마
		- `@Deprecated(since = "2.4", forRemoval = true)` -> 진짜진짜 쓰지마
- `@SuppressWarnings`
	- 자바 컴파일러가 문제를 경고하지만, 개발자가 문제를 잘 알고 있으니 경고하지 말라고 지시
	- **왠만하면 사용 X** (제네릭 쓰다보면 개발자가 책임지겠다고 쓸 때 정도 있음)
	- 옵션
		- `all`: 모든 경고 억제
		- `deprecation`: deprecated 코드를 사용할 때 발생하는 경고 억제
		- `unchecked`: 제네릭 타입과 관련된 unchecked 경고 억제
		- `serial`: Serializable 인터페이스를 구현할 때 serialVersionUID 필드를 선언하지 않은 경우 발생하는 경고 억제
		- `rawtypes`: 제네릭 타입이 명시되지 않은(raw) 타입을 사용할 때 발생하는 경고 억제
		- `unused`: 사용되지 않는 변수, 메서드, 필드 등을 선언했을 때 발생하는 경고 억제

## 참고: 애노테이션 기반 검증기 활용 예제
```java
public class Validator {

    public static void validate(Object obj) throws Exception {
        Field[] fields = obj.getClass().getDeclaredFields();

        for (Field field : fields) {
            field.setAccessible(true);

            // @NotEmpty 어노테이션 검사
            if (field.isAnnotationPresent(NotEmpty.class)) {
                String value = (String) field.get(obj);
                NotEmpty annotation = field.getAnnotation(NotEmpty.class);
                if (value == null || value.isEmpty()) {
                    throw new RuntimeException(annotation.message());
                }
            }
            // @Range 어노테이션 검사
            if (field.isAnnotationPresent(Range.class)) {
                long value = field.getLong(obj);
                Range annotation = field.getAnnotation(Range.class);
                if (value < annotation.min() || value > annotation.max()) {
                    throw new RuntimeException(annotation.message());
                }
            }
        }
    }
}
```

