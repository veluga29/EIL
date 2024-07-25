## 스프링 타입 변환
- HTTP 요청 데이터는 **문자**로 처리됨
- 다만, 파라미터를 원하는 타입으로 지정하면 **스프링이 자동으로 타입 변환**
	- 개발자는 직접 타입 변환할 필요 없이 원하는 타입으로 편리하게 전달 받아 사용
	- 예시
		- `@RequestParam`, `@ModelAttribute`, `@PathVariable` ... (스프링 MVC 요청 파라미터)
		- `@Value` 등으로 YML 정보 읽기
		- XML에 넣은 스프링 빈 정보 변환
		- 뷰 렌더링 시
- 컨버터 인터페이스
	```java
	package org.springframework.core.convert.converter;
	
	public interface Converter<S, T> {
	    T convert(S source);
	}
	```
	- 스프링은 **확장 가능한 컨버터 인터페이스** 지원
	- **`org.springframework.core.convert.converter.Converter`**
	- 개발자는 스프링에 **추가적인 타입 변환이 필요**하면 **인터페이스를 구현해 등록**

>`Converter`와 `PropertyEditor`
>
>과거에는 `PropertyEditor`로 타입 변환했으나 동시성 문제가 있어서 잘 사용하지 않는다. 지금은 기능 확장 시 `Converter`를 사용한다.

>스프링이 제공하는 다양한 컨버터
>
>- `Converter`: 기본 타입 컨버터
>- `ConverterFactory`: 전체 클래스 계층 구조가 필요할 때 
>- `GenericConverter`: 정교한 구현, 대상 필드의 애노테이션 정보 사용 가능
>- `ConditionalGenericConverter`: 특정 조건이 참인 경우에만 실행
>  
>  스프링은 문자, 숫자, 불린, Enum 등 일반적인 타입에 대한 대부분의 인터페이스 구현체들을 제공한다.

## ConversionService
- 스프링은 **개별 컨버터를 모아서 묶어두고 편리하게 사용**할 수 있도록 제공
- `ConversionService` 인터페이스
	```java
	public interface ConversionService {
	    boolean canConvert(@Nullable Class<?> sourceType, Class<?> targetType);
	    boolean canConvert(@Nullable TypeDescriptor sourceType, TypeDescriptor targetType);
	    
	    <T> T convert(@Nullable Object source, Class<T> targetType);
	    Object convert(@Nullable Object source, @Nullable TypeDescriptor sourceType, TypeDescriptor targetType);
	}
	```
	- `canConvert`: 컨버팅이 가능한지 체크
	- `convert`: 실제 컨버팅 수행
- `ConversionService`는 2가지 이점 제공
	- 등록과 사용 분리
		- 컨버터 등록 입장: 타입 컨버터를 명확히 알아야 함
		- 컨버터 사용 입장: 컨버전 서비스 인터페이스만 의존, 구체적인 타입 컨버터 몰라도 됨
	- 인터페이스 분리 원칙 적용 (ISP)
		- 