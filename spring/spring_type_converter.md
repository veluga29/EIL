## 스프링 Converter
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
- **스프링 내부**에서도 `ConversionService`를 사용해 타입 변환
	- e.g `@RequestParam`
	- `RequestParamMethodArgumentResolver`에서 `ConversionService` 사용해 타입 변환
- 뷰 템플릿도 컨버젼 서비스 적용 가능 (타임 리프 문법: `${{...}}`)
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
	- **등록과 사용 분리**
		- 컨버터 등록 입장: 타입 컨버터를 명확히 알아야 함
		- 컨버터 사용 입장: 컨버전 서비스 인터페이스만 의존, 구체적인 타입 컨버터 몰라도 됨
	- **인터페이스 분리 원칙** 적용 (ISP)
		- `DefaultConversionService` 구현체는 두 인터페이스를 구현
			- `ConversionService`: 컨버터 사용에 초점
			- `ConverterRegistry`: 컨버터 등록에 초점
		- `ConversionService` 인터페이스를 사용하는 **클라이언트는 꼭 필요한 메서드만 알게 됨**

>메시지 컨버터와 컨버전 서비스
>
>**`HttpMessageConverter`에는 컨버전 서비스가 적용되지 않는다.**
>메시지 컨버터는 **Jackson 라이브러리**를 사용해 HTTP 메시지 바디 내용을 객체로 변환하거나 객체를 HTTP 메시지 바디에 입력한다. 
>따라서, JSON 결과로 만들어지는 숫자나 날짜 포멧을 변경하고 싶으면, **Jackson 라이브러리가 제공하는 설정을 통해 지정**해야 한다. (Jackson Data Format 같은 키워드로 검색 필요)
>
>참고로, **컨버전 서비스는 `@RequestParam` , `@ModelAttribute` , `@PathVariable` , 뷰 템플릿 등에서 사용**할 수 있다.

## Formatter
- **일반적인 웹 애플리케이션 환경**에서의 타입 변환 (개발자)
	- 문자 -> 객체 (다른 타입)
	- 객체 (다른 타입) -> 문자
- `Formatter`는 **문자에 특화**한 `Converter`의 특별한 버전
	- `Formatter`: **문자 특화** + **현지화(`Locale`)**
	- `Converter`: 범용 (객체 -> 객체)
- `Formatter` 인터페이스
	```java
	public interface Printer<T> {
	    String print(T object, Locale locale);
	}
	
	public interface Parser<T> {
	    T parse(String text, Locale locale) throws ParseException;
	}
	
	public interface Formatter<T> extends Printer<T>, Parser<T> {
	}
	```
	- `String print(T object, Locale locale)` : 객체를 문자로 변경
	- `T parse(String text, Locale locale)` : 문자를 객체로 변경

>스프링 `Formatter`
>
>스프링은 용도에 따라 다양한 방식의 포멧터를 제공한다.
>- `Formatter`: 포멧터
>- `AnnotationFormatterFactory`: 필드 타입 혹은 애노테이션 정보를 활용할 수 있는 포멧터

## FormattingConversionService
- **포멧터를 지원**하는 컨버전 서비스
	- 포멧터도 특별한 컨버터일 뿐
	- 내부에서 어댑터 패턴을 사용해 `Formatter`가 `Converter`처럼 동작하도록 지원
- **컨버터 & 포멧터를 모두 등록 가능**
	- `FormattingConversionService` 는 `ConversionService` 관련 기능을 상속 받음
- `DefaultFormattingConversionService` 구현체: 기본적인 통화, 숫자 기본 포멧터를 추가해 제공
## 커스텀Converter 및 Formatter 등록
```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

	@Override
	public void addFormatters(FormatterRegistry registry) {
		//컨버터 추가
		registry.addConverter(new StringToIpPortConverter());
		registry.addConverter(new IpPortToStringConverter());
		
		//포멧터 추가
        registry.addFormatter(new MyNumberFormatter());
	}
}
```
- 등록 방법은 다르지만 **컨버전 서비스를 통해 컨버터와 포멧터를 일관성 있게 사용 가능**
- `addFormatters()` 오버라이딩 -> 스프링 내부 `ConversionService`에 **컨버터 자동 등록**
	- `addConverter()`: 컨버터 추가
	- `addFormatter()`: 포멧터 추가
- 우선 순위
	- 스프링 기본 컨버터보다 **추가한 컨버터가 높은 우선순위**
	- 포멧터보다 **컨버터가 높은 우선 순위**
## 스프링 기본 포멧터
- 스프링은 자바 기본 타입들에 대한 수많은 포멧터를 기본 제공
- **애노테이션 기반 포멧터**도 제공
	- 덕분에 객체에 **각 필드마다 다른 형식으로 포멧** 지정 가능
	- 종류
		- `@NumberFormat`
			- 숫자 관련 형식 지정
			- `NumberFormatAnnotationFormatterFactory`
		- `@DateTimeFormat`
			- 날짜 관련 형식 지정
			- `Jsr310DateTimeFormatAnnotationFormatterFactory`
	- 예시 코드
		```java
		@Controller
		public class FormatterController {
		    
		    @GetMapping("/formatter/edit")
		    public String formatterForm(Model model) {
		        Form form = new Form();
		        form.setNumber(10000);
		        form.setLocalDateTime(LocalDateTime.now());
		        model.addAttribute("form", form);
		        return "formatter-form";
		    }
		    
		    @PostMapping("/formatter/edit")
		    public String formatterEdit(@ModelAttribute Form form) {
		        return "formatter-view";
		    }
		    
		    @Data
		    static class Form {
		         
		        @NumberFormat(pattern = "###,###")
		        private Integer number;
			    
			    @DateTimeFormat(pattern = "yyyy-MM-dd HH:mm:ss")
		        private LocalDateTime localDateTime;
		    }
		}
		```

***
## Reference
*[스프링 MVC 2편 - 백엔드 웹 개발 활용 기술](https://www.inflearn.com/course/%EC%8A%A4%ED%94%84%EB%A7%81-mvc-2#)*