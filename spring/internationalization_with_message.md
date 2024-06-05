## 메시지
- 다양한 메시지를 **한 곳에서 관리**하도록 하는 기능
- **스프링 부트는 `messages.properties`를 기본 메시지 파일로 인식**하고 관리
- 경로: `/resources/messages.properties`
## 국제화
- **메시지 파일을 각 나라 언어별로 별도 관리해** 서비스를 국제화
	- **`베이스파일명_언어`** 형식으로 메시지 파일을 만들어두면 **자동으로 인식**
		- e.g. `messages_en.properties`, `messages_ok.properties`
	- 기본은 HTTP **`accept-language`** 헤더 값을 보고 판단
		- 스프링 부트 기본 `LocaleResolver`: **`AcceptHeaderLocaleResolver`**
	- 혹은 **사용자가 직접 언어를 선택**하도록 하고, 쿠키나 세션 기반 처리도 가능
		- **`LocaleResolver`** 인터페이스의 **구현체 변경**
			```java
			public interface LocaleResolver {
				 
				 Locale resolveLocale(HttpServletRequest request);
				 
				 void setLocale(HttpServletRequest request, @Nullable HttpServletResponse
			 response, @Nullable Locale locale);
			
			}
			```
- 찾을 수 있는 **국제화 파일이 없는 경우**, **언어정보 없는 디폴트 파일 기본** 사용 (`messages.properties`)
## **`MessageSource`** 인터페이스
```java
public interface MessageSource {

     String getMessage(String code, @Nullable Object[] args, @Nullable String
 defaultMessage, Locale locale);
 
     String getMessage(String code, @Nullable Object[] args, Locale locale)
throws NoSuchMessageException;
```
- 스프링은 메시지 관리 기능을 **`MessageSource`** **인터페이스**를 통해 제공
	- **`getMessage`**: 파라미터만 다르고 **메시지를 읽어오는 기능** 수행
		- 메시지가 없는 경우 `NoSuchMessageException` 발생
	- `code`: 메시지 파일에서 지정한 **키**
	- `args`: **매개변수를 전달**하는 배열
		- 메시지 파일의 `{0}` 부분 치환
			- `hello.name=안녕 {0}`
		- `Object[]` 배열로 넘겨야 함
			- `ms.getMessage("hello.name", new Object[]{"Spring"}, null);`
	- `defaultMessage`: 메시지가 없을 때 **기본 메시지** 지정 (예외 발생 예방)
	- `locale`: **국제화 파일 선택**
		- e.g. `Locale.KOREA`, `Locale.ENGLISH`
		- 정보가 없을 시 (`null`) `Locale.getDefault()` 호출해, 시스템 기본 로케일 사용
			- 시스템 기본 로케일 조회 실패 시 기본 이름 메시지 파일 조회 (`message.properties`)
		- `Locale`이 `en_US`인 경우 `messages_en_US` / `messages_en` / `messages` 순서로 찾음
- **스프링 부트는 자동으로 `ResourceBundleMessageSource` 구현체를 스프링 빈으로 등록**
	- **메시지 파일 임의 지정** 방법 (**`application.properties`**)
		- e.g. `spring.messages.basename=messages,config.i18n.messages`
		- 기본값: `spring.messages.basename=messages`
- 직접 등록 방법
	```java
	@Bean
	public MessageSource messageSource() {
		ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
		messageSource.setBasenames("messages", "errors");
		messageSource.setDefaultEncoding("utf-8");
		
		return messageSource;
	```
	- `basenames`: 설정 파일 이름 지정
		- **여러 파일을 한 번에 지정 가능** (`messages`, `errors`)
		- `messages.properties`, `errors.properties` 파일을 읽음
	- `defaultEncoding`: 인코딩 정보 지정 (**`utf-8`** 사용하면 됨)