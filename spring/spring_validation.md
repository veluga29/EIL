## Validation
- **HTTP 요청이 정상인지 검증**하는 것은 **컨트롤러의 중요한 역할**
- 스프링 제공 방법
	- **Bean Validation** (Bean Validation 2.0 (JSR-380)) + **`BindingResult`**
		- **검증 로직을 공통화 및 표준화**해 객체에 **애노테이션**으로 **검증** 적용
			- 객체에 **검증 애노테이션 적용** (e.g. `@NotNull`, `@Range`...)
			- 파라미터에 **`@Valid`**, **`@Validated`** 적용하면 **검증 실행**
		- 기술 **표준**으로서 검증 애노테이션 및 여러 인터페이스의 모음
			- **구현체**는 일반적으로 **하이버네이트 Validator**를 사용 (ORM과 관련 없음)
		- **스프링 MVC**가 **Bean Validator**를 사용하는 **과정**
			- `spring-boot-starter-validation`를 **라이브러리로 등록**
			- 스프링 부트가 **자동으로 Bean Validator를 인지**하고 **스프링에 통합**
			- 스프링 부트는 `LocalValidatorFactoryBean`을 **글로벌 Validator로 등록**
				- **애노테이션**을 보고 **검증을 수행**하는 검증기 (e.g. `@NotNull`)
			- **`@Valid`**, **`@Validated`** 적용으로 파라미터 검증 실행
			- **검증 오류 발생** 시 `FieldError`, `ObjectError` 생성해 **`BindingResult`** 담음
			- 검증 순서
				- 타입에 맞춰 각각의 **필드 바인딩 시도**
					- **실패**시 **`typeMismatch`로 `FieldError` 추가**
				- **바인딩에 성공한 필드만 Bean Validation 적용**
		- **비즈니스 로직 적용 방법**
			- **컨트롤러 용도에 따라 검증 전용 객체 분리하기** (도메인 객체는 순수하게 유지)
				- 장점: 검증 중복이 없고 복잡도 낮음
				- 단점: 컨트롤러에서 전송 받은 데이터를 도메인 객체 생성 및 변환 과정 추가
				- **검증 전용 객체 이름**은 **일관성**만 있게자유로이 명명
					- `ItemSave`, `ItemSaveForm` , `ItemSaveRequest` ,`ItemSaveDto`...
			- 동일한 도메인 객체 사용 + Bean Validation의 `groups` 속성으로 분류 - 권장 X
				- 장점: 중간에 도메인 객체 생성 과정 없이 컨트롤러부터 리포지토리까지 전달 가능
				- 단점: 간단한 경우에만 가능
				- 검증할 기능을 등록 및 수정 등 각각의 그룹으로 나누어 적용 가능
					- 저장용 groups 생성
						```java
						package hello.itemservice.domain.item;
						
						public interface SaveCheck {
						}
						```
					- 수정용 groups 생성
						```java
						package hello.itemservice.domain.item;
						
						public interface UpdateCheck {
						}
						```
					- groups 적용
						```java
						@Data
						public class Item {
						
						    @NotNull(groups = UpdateCheck.class) //수정시에만 적용
						    private Long id;
						    
						    @NotBlank(groups = {SaveCheck.class, UpdateCheck.class})
						    private String itemName;
						    
						    @NotNull(groups = {SaveCheck.class, UpdateCheck.class})
						    @Range(min = 1000, max = 1000000, groups = {SaveCheck.class, UpdateCheck.class})
							private Integer price;
						
							@NotNull(groups = {SaveCheck.class, UpdateCheck.class})
							@Max(value = 9999, groups = SaveCheck.class) //등록시에만 적용 
							private Integer quantity;
						    
						    public Item() {
						    }
						    
						    public Item(String itemName, Integer price, Integer quantity) {
						        this.itemName = itemName;
						        this.price = price;
						        this.quantity = quantity;
							} 
						}
						```
					- `@Validated`에 groups 적용 (`@Valid`는 groups 기능이 없음)
						- `@Validated(SaveCheck.class)`
						- `@Validated(UpdateCheck.class)`
		- **`ObjectError`** 처리 방법
			- **글로벌 오류는 자바 코드로 직접 작성해 처리 권장** (메서드 추출)
				```java
				public String addItem(@Validated @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes) {
					//특정 필드 예외가 아닌 전체 예외
					if (item.getPrice() != null && item.getQuantity() != null) {
				        int resultPrice = item.getPrice() * item.getQuantity();
				        if (resultPrice < 10000) {
				            bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
						}
					}
					...
				}
				```
			- `@ScriptAssert()` - 권장 X
				```java
				@ScriptAssert(lang = "javascript", script = "_this.price * _this.quantity >= 10000")
				public class Item {
					//...
				}
				```
				- 생성되는 메시지 코드
					- `ScriptAssert.item`
					- `ScriptAssert`
				- 제약이 많고 복잡하여 권장하지 않음
		- **API 적용** 시 고려할 점 (**HTTP 메시지 컨버터**)
			- **`@Valid`**, **`@Validated`** -> **`HttpMessageConverter`**(**`@RequestBody`**)에 **적용 가능**
				- `public Object addItem(@RequestBody @Validated ItemSaveForm form, BindingResult bindingResult)`
			- API는 3가지 경우 고려 필요
				- **성공 요청**
				- **실패 요청**
					- **`HttpMessageConverter`** 에서 **요청 JSON을 객체로 생성하는데 실패**
					- **컨트롤러 자체가 호출되지 않고 예외가 발생**
					- 검증 적용(Validator) X
				- **검증 오류 요청**
					- **JSON 객체 생성은 성공**했으나 **이후 검증 실패**
					- `HttpMessageConverter` 는 성공하지만 검증(Validator)에서 오류가 발생
		- 필요한 의존관계 패키지
			- `implementation 'org.springframework.boot:spring-boot-starter-validation`
				- `jakarta.validation-api`: Bean Validation 인터페이스 
				- `hibernate-validator`: 구현체
		- 검증 애노테이션
			- `@NotBlank`: `null` 과 `""` 과 `" "` 모두 허용하지 않음
			- `@NotEmpty`: `null` 과 `""`을 허용하지 않음
			- `@NotNull`: `null`을 허용하지 않음
			- `@Range(min = 1000, max = 1000000)`: 범위 안의 값만 허용
			- `@Max(9999)`: 최대 지정 값까지만 허용
		- 테스트에서 Bean Validation 사용하기
			- `ValidatorFactory factory = Validation.buildDefaultValidatorFactory();`
			- `Validator validator = factory.getValidator();`
			- `Set<ConstraintViolation<Item>> violations = validator.validate(item);`
		- Bean Validation 오류 코드
			- Bean Validation이 **오류 메시지를 찾는 순서**
				- `MessageResolver` 생성 메시지 코드대로 **`messageSource`** 찾기
				  (`errors.properties`)
				- **애노테이션**의 **`message` 속성** 사용
					- `@NotBlank(message = "공백! {0}")`
				- 라이브러리가 제공하는 **기본 값** 사용
			- 기본은 **애노테이션 이름**으로 오류코드를 등록
				- e.g. `@NotBlank`
					- `NotBlank.item.itemName`
					- `NotBlank.itemName`
					- `NotBlank.java.lang.String`
					- `NotBlank`
			- 생성이 예상되는 적절한 오류 코드로 `errors.properties`에 **원하는 메시지 등록 가능**
	- `BindingResult`
		- **스프링**이 제공하는 **검증 오류를 보관하는 객체** (Model에 자동 포함)
		- 실제로는 **인터페이스**이고 `Errors` 인터페이스를 상속받고 있음
			- 실제 넘어오는 구현체는 `BeanPropertyBindingResult`
			- 타입으로 `Errors`를 사용해도 되지만 `BindingResult`는 더 추가적인 기능 제공
			- 관례상으로도 `Errors`보다 `BindingResult` 많이 사용
		- 반드시 **`@ModelAttribute`** 파라미터의 **바로 뒤**에 위치해야 함
			- e.g.  `@ModelAttribute Item item, BindingResult bindingResult`
		- 타임리프가 통합 기능도 제공 (`#fields`, `th:errors`, `th:errorclass`)
		- 검증 오류 적용 방법
			- 스프링 자동 적용
				- `@ModelAttribute`에 **데이터 바인딩 오류**가 발생 시 자동 처리
				- e.g. 주로 타입 오류
				- `BindingResult`가 없으면
					- 컨트롤러 호출 X, 400 오류 페이지 이동
				- `BindingResult`가 있으면
					- 스프링이 **`new FieldError()`** 실행
					- 생성한 필드 에러 객체를 **`BindingResult`에 자동으로 담음**
					- 이후 **컨트롤러 정상 호출**
			- 개발자가 직접 넣기
				- `rejectValue()`, `reject()`를 호출하는 방법
					- **`target`**(**검증 대상 모델**)을 `BindingResult`가 **이미 앎** (깔끔한 코드)
					- **내부에서 `MessageCodesResolver`를 사용**
						- `FieldError`, `ObjectError` 생성 후 오류 코드들을 보관
						- 즉, `MessageCodesResolver`가 생성한 오류들을 가지고 처리
					- 필드 에러 처리 (**`rejectValue()`**)
						```java
						bindingResult.rejectValue("quantity", "max", new Object[]{9999}, null);
						```
						- **`rejectValue()`** 파라미터
							- `field`: 오류 필드명
							- `errorCode`
								- `messageCodesResolver`를 위한 오류 코드
								- 필드명, 오브젝트명, 오류코드를 조합한 키로 메시지 가져옴
							- `errorArgs`: 메시지에서 사용하는 인자
							- `defaultMessage`: 오류 메시지 찾을 수 없을 때 기본 메시지
					- 글로벌 에러 처리 (**`reject()`**)
						```java
						bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
						```
						- **`reject()`** 파라미터
							- `errorCode`
							- `errorArgs`
							- `defaultMessage`
					- 참고
						- `ValidationUtils`: Empty, 공백 등의 조건까지 한 줄로 처리 가능
						- `ValidationUtils.rejectIfEmptyOrWhitespace(bindingResult, "itemName", "required");`
				- `FieldError`, `ObjectError` 직접 생성을 통한 방법 (`addError()`)
					- 필드 에러 처리 (**`FieldError`**)
						- **`FieldError`** 객체를 생성해 bindingResult에 담음
						- `FieldError`는 **`ObjectError`의 자식**
							```java
							bindingResult.addError(new FieldError("item", "itemName", "상품 이름은 필수입니다."));
							```
						- **`FieldError`** 파라미터 (생성자 2개)
							- **`objectName`**: `@ModelAttribute` 이름
							- **`field`**: 오류가 발생한 필드 이름
							- **`rejectedValue`**: **사용자가 입력한 값** (거절된 값)
							- `bindingFailure`: 바인딩 실패인지, 검증 실패인지 구분 값
							- **`codes`**: 메시지 코드 지정 (`errors.properties`)
								- 배열로 여러 값을 전달 가능
								- 순서대로 매칭해 처음 매칭되는 메시지 사용 
								  (없으면 예외 발생)
								- e.g. `new String[] {"max.item.quantity"}`
							- `arguments`: 메시지에서 사용하는 인자
								- `{0}`, `{1}`... 순서대로 치환 값 전달
								- e.g. `new Object[] {9999}`
							- **`defaultMessage`**: 오류 기본 메시지
					- 글로벌 에러 처리 (**`ObjectError`** - 특정 필드를 넘어서는 오류)
						- **`ObjectError`** 객체를 생성해 bindingResult에 담음
							```java
							bindingResult.addError(new ObjectError("item", "가격 * 수량의 합은 10,000원 이상이어야 합니다. 현재 값 = " + resultPrice));
							```
						- **`ObjectError`** 파라미터 (생성자 2개)
							- **`objectName`**: `@ModelAttribute` 이름
							- **`codes`**: 메시지 코드
							- `arguments`: 메시지에서 사용하는 인자
							- **`defaultMessage`**: 오류 기본 메시지
			- `Validator` 사용하기
				- **스프링**이 제공하는 **`Validator` 인터페이스**를 상속해 검증 로직을 담기 가능
				- 컨트롤러에서 **검증 로직을 분리**하고 **재사용**할 수 있음
				- 구현할 메서드
					- `supports`
						- 해당 검증기 지원 여부 확인
							```java
							@Override
							public boolean supports(Class<?> clazz) {
							    return Item.class.isAssignableFrom(clazz);
							}
							```
					- `validate(Object target, Errors errors)`
						- 검증 대상 객체와 `BindingResult` 전달
				- 적용 방법
					- **`WebDataBinder`** 에 검증기 추가 + **`@Validated`** 파라미터 적용
						```java
						@InitBinder
						 public void init(WebDataBinder dataBinder) {
						     log.info("init binder {}", dataBinder);
						     dataBinder.addValidators(itemValidator);
						 }
						```
						- **`WebDataBinder`** (**컨트롤러에 추가**)
							- **스프링 파라미터 바인딩 역할 및 검증** 기능 수행
							- **해당 컨트롤러가 호출될 때마다 검증기 적용**
								- 즉, 요청이 올 때마다 새로 생성해 검증
							- 글로벌 설정도 가능하지만 사용할 일 거의 없음 (권장 X)
								```java
								@SpringBootApplication
								public class ItemServiceApplication implements WebMvcConfigurer {
									public static void main(String[] args) {
										SpringApplication.run(ItemServiceApplication.class, args);
									}
									
									@Override
									public Validator getValidator() {
										return new ItemValidator();
									}
								}
								```
							- 글로벌 설정 시 **`BeanValidator`가 자동 등록되지 않음**
						- **`@Validated`**
							- 검증 실행을 원하는 파라미터에 적용
							- `supports`를 통해 등록된 검증기들 중 실행해야 할 것을 구분
					- 검증기 직접 호출도 가능하지만 불편
- 개발자 직접 처리
	- **중복 처리**가 많아짐
	- **타입 오류 처리가 안됨**
		- Integer 타입 파라미터에 문자가 들어오면 오류
		- 스프링 MVC에서 컨트롤러 호출되기 전부터 400 예외 발생
	- 특히, **타입 오류의 경우 검증 전 고객의 입력 데이터를 보존하지 못함** (UX에 중요한 부분)

>클라이언트 검증 & 서버 검증
>
>클라이언트 검증만 사용하면 **조작이 가능해 보안에 취약**하고, 서버 검증만 있다면 **즉각적인 고객 사용성이 부족**해진다.
>따라서, **클라이언트 검증과 서버 검증은 둘 다 적절히 섞어 사용**하되, **최종적으로 서버 검증을 필수로 진행**한다.

>`@Validated`와 `@Valid`
>
>검증 시 `@Validated`, `@Valid` 둘 다 사용 가능하다.
>`@Validated` 는 스프링 전용 검증 애노테이션이고, `@Valid` 는 자바 표준 검증 애노테이션이다.
>
>다만, `@Valid`는 다음 의존관계 추가가 필요하다.
>
>`implementation 'org.springframework.boot:spring-boot-starter-validation'

>`javax.validation` VS `org.hibernate.validator`
>
>`javax.validation`으로 시작하면 표준 인터페이스, `org.hibernate.validator`로 시작하면 하이버네이트 validator 구현체를 사용할 때만 제공되는 검증이다.
>다만, **실무에서 대부분 하이버네이트 validator를 사용**하므로 **자유롭게 사용**해도 된다.

>BeanValidation - `@ModelAttribute` VS `@RequestBody`
>
>**`@ModelAttribute`** 는 **필드 단위로 정교하게 바인딩**이 적용된다. 특정 필드가 바인딩 되지 않아도(타입이 맞지 않는 오류) **나머지 필드는 정상 바인딩** 되고, Validator를 사용한 **검증도 적용**할 수 있다.
>**`@RequestBody`** 는 **객체 단위로 바인딩**이 적용된다. `HttpMessageConverter` 단계에서 **JSON 데이터를 객체로 변경하지 못하면 이후 단계 자체가 진행되지 않고 예외가 발생**한다. 컨트롤러도 호출되지 않고, Validator도 적용할 수 없다.

## MessageCodesResolver
- 검증 오류 코드로 **메시지 코드 후보들을 생성**
- `MessageCodesResolver`는 인터페이스이고 **`DefaultMessageCodesResolver`가 기본 구현체**
- 보통 이렇게 **생성된 메시지 코드를 기반**으로 **`MessageSource`에서 메시지를 찾음**
- 기본 메시지 코드 생성 규칙
	- 객체 오류
		- 규칙
			1. code + "." + object name 
			2. code
		- e.g. 오류 코드: `required`, object name: `item`
			1. `required.item`
			2. `required`
	- 필드 오류
		- 규칙
			1. code + "." + object name + "." + field 
			2. code + "." + field
			3. code + "." + field type
			4. code
		- 예) 오류 코드: `typeMismatch`, object name: `"user"`, field: `"age"`, field type: `int`
			1. `typeMismatch.user.age`
			2. `typeMismatch.age`
			3. `typeMismatch.int`
			4. `typeMismatch`
- **메시지 처리 전략** 예시 (**`errors.properties`**)
	```yml
	#==ObjectError==
	#Level1
	totalPriceMin.item=상품의 가격 * 수량의 합은 {0}원 이상이어야 합니다. 현재 값 = {1}
	
	#Level2
	totalPriceMin=전체 가격은 {0}원 이상이어야 합니다. 현재 값 = {1}
	
	#==FieldError==
	#Level1
	required.item.itemName=상품 이름은 필수입니다. 
	range.item.price=가격은 {0} ~ {1} 까지 허용합니다. 
	max.item.quantity=수량은 최대 {0} 까지 허용합니다.
	
	#Level2 - 생략
	
	#Level3
	required.java.lang.String = 필수 문자입니다. 
	required.java.lang.Integer = 필수 숫자입니다. 
	min.java.lang.String = {0} 이상의 문자를 입력해주세요. 
	min.java.lang.Integer = {0} 이상의 숫자를 입력해주세요. 
	range.java.lang.String = {0} ~ {1} 까지의 문자를 입력해주세요. 
	range.java.lang.Integer = {0} ~ {1} 까지의 숫자를 입력해주세요. 
	max.java.lang.String = {0} 까지의 문자를 허용합니다.
	max.java.lang.Integer = {0} 까지의 숫자를 허용합니다.
	
	#Level4
	required = 필수 값 입니다.
	min= {0} 이상이어야 합니다.
	range= {0} ~ {1} 범위를 허용합니다.
	max= {0} 까지 허용합니다.
	```

>메시지 처리 기본
>
>범용 메시지를 두고, 세밀하게 작성해야 하는 경우에 세밀한 메시지를 적용하도록 메시지 단계를 두자
>**세밀한 메시지가 범용 메시지보다 우선순위 가진다.**
>
>예를 들어, `required`라는 메시지만 있으면 해당 메시지를 기본으로 사용하고, `required.item.itemName` 같이 세밀한 메시지 코드가 있으면 이 메시지를 높은 우선순위로 사용한다.
>
>**`MessageCodesResolver`는 메시지 관련 공통 전략을 편리하게 적용할 수 있게 지원한다.**

>스프링 타입 오류
>
>스프링은 타입 오류가 발생하면 **`typeMismatch`라는 오류 코드**를 자동으로 사용한다.
>이 경우 `MessageCodesResolver`를 통해 4가지 메시지 코드가 발생할텐데, `errors.properties`에 해당 코드가 없다면 **스프링이 생성한 기본 메시지가 출력**된다.
>
>`Failed to convert property value of type java.lang.String to required type java.lang.Integer for property price; nested exception is java.lang.NumberFormatException: For input string: "A"`
>
>기본 출력을 **임의로 바꾸고 싶다면**, `errors.properties`에 다음과 같은 **코드를 적절하게 추가**하면 된다.
>
>`typeMismatch.java.lang.Integer=숫자를 입력해주세요.`
>`typeMismatch=타입 오류입니다.`

## Reference
[@NotNull, @NotEmpty, @NotBlank 의 차이점 및 사용법](https://sanghye.tistory.com/36)