## 서블릿 예외 처리
- 순수 서블릿 컨테이너는 2가지 방식으로 예외 처리 지원
	- `Exception`
		- **WAS**(**여기까지 전파**) <- 필터 <- 서블릿 <- 인터셉터 <- **컨트롤러**(**예외발생**)
			- **웹 애플리케이션**은 서블릿 컨테이너 안에서 실행
			- 애플리케이션 내에서 예외를 잡지 못하고 **서블릿 밖으로 WAS까지 예외가 전달**되는 상황
			- **WAS**(**톰캣**)가 **500 상태코드**로 처리 (서버에서 처리할 수 없는 오류로 판단)
				- 스프링 부트 사용시 **스프링 부트 기본 예외 페이지** 보여줌
				- 아닐 시 **tomcat 기본 제공 오류 화면**
				- 참고) 없는 자원 접근 시 스프링 부트 혹은 tomcat의 404 예외 페이지 보여줌
	- `response.sendError(HTTP 상태 코드, 오류 메시지)`
		- **WAS**(sendError 호출 기록 확인) <- 필터 <- 서블릿 <- 인터셉터 <- **컨트롤러** (**response.sendError()**)
			- 호출 시 **`response` 내부**에 **오류가 발생했다는 상태**를 저장
			- 서블릿 컨테이너는 **응답 전에 해당 상태를 보고** 오류 코드에 맞는 **기본 오류 페이지** 제공
		- `HttpServletResponse` 메서드
		- 호출 시 **서블릿 컨테이너에게 오류가 발생했다고 전달**
		- 실제로 예외가 발생하지는 않고 **정상 리턴으로 WAS까지 전달**
- 서블릿 오류 페이지 등록 (스프링 부트를 통한 커스터마이징)
	- `ErrorPage` 설정
		```java
		@Component
		public class WebServerCustomizer implements WebServerFactoryCustomizer<ConfigurableWebServerFactory> {
		    
		    @Override
		    public void customize(ConfigurableWebServerFactory factory) {
		    
		        ErrorPage errorPage404 = new ErrorPage(HttpStatus.NOT_FOUND, "/error-page/404");
		        ErrorPage errorPage500 = new ErrorPage(HttpStatus.INTERNAL_SERVER_ERROR, "/error-page/500");
				ErrorPage errorPageEx = new ErrorPage(RuntimeException.class, "/error-page/500");
		        
		        factory.addErrorPages(errorPage404, errorPage500, errorPageEx);
			} 
		}
		```
		- `sendError`, `Exception` 발생 상황에 따라 설정한 컨트롤러 URL 호출
	- 오류 페이지 컨트롤러
		```java
		@Slf4j
		@Controller
		public class ErrorPageController {
		
			@RequestMapping("/error-page/404")
		    public String errorPage404(HttpServletRequest request, HttpServletResponse response) {
		        log.info("errorPage 404");
		        return "error-page/404";
		    }
		
			@RequestMapping("/error-page/500")
		    public String errorPage500(HttpServletRequest request, HttpServletResponse response) {
		        log.info("errorPage 500");
		        return "error-page/500";
		    }
		}
		```
		- 컨트롤러가 개발자가 만든 오류 페이지 뷰 리턴
- **오류 페이지 작동 원리**
	- 예외 발생과 오류 페이지 요청 흐름
		- WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
		- WAS `/error-page/500` **다시 요청** -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러(`/error-page/1`) -> View
	- 정상 요청, 예외 발생과 오류 페이지 요청 흐름 (Ver. 필터, 인터셉터 중복 호출 제거)
		- WAS(/error-ex, dispatchType=REQUEST) -> 필터 -> 서블릿 -> 인터셉터 -> 컨트롤러
		- WAS(여기까지 전파) <- 필터 <- 서블릿 <- 인터셉터 <- 컨트롤러(예외발생)
		- WAS 오류 페이지 확인
		- WAS(/error-page/500, dispatchType=ERROR) -> 필터(x) -> 서블릿 -> 인터셉터(x) -> 컨트롤러(/error-page/500) -> View 
	- 고객(클라이언트)은 한 번 요청하지만, **서버 내부적으로 컨트롤러 2번 호출**
		- 재호출 시 WAS는 오류 정보를 `request` 객체에 담아 보내줌 (`setAttribute()`)
		- 오류정보는 다음 키에 대응
			- `javax.servlet.error.exception` : 예외 
			- `javax.servlet.error.exception_type` : 예외 타입 
			- `javax.servlet.error.message` : 오류 메시지 
			- `javax.servlet.error.request_uri` : 클라이언트 요청 URI 
			- `javax.servlet.error.servlet_name` : 오류가 발생한 서블릿 이름 
			- `javax.servlet.error.status_code` : HTTP 상태 코드
	- 오류 페이지 출력을 위한 **내부 요청**은 **보통 필터, 인터셉터 중복 호출을 피하는게 효율적**
		- 필터는 **`DispatcherType`** 통해 중복 호출 제거 (`dispatchType=REQUEST`)
			- 서블릿은 요청 종류를 구분할 수 있도록 **`DispatcherType`** 제공 
			  (`HttpServletRequest`에 포함)
				- **`DispatcherType.REQUEST`**: 클라이언트 요청 (필터의 **기본값**)
				- **`DispatcherType.ERROR`**: 오류 요청
				- `DispatcherType.FORWARD`: 서블릿에서 다른 서블릿이나 JSP를 호출할 때
				- `DispatcherType.INCLUDE`: 서블릿에서 다른 서블릿이나 JSP의 결과 포함할 때
				- `DispatcherType.ASYNC`: 서블릿 비동기 호출
			- 필터 호출 여부 등록 예시
				- `filterRegistrationBean.setDispatcherTypes(DispatcherType.REQUEST, DispatcherType.ERROR);`
				- 이 경우, 클라이언트 요청과 오류 페이지 요청 모두 필터 호출
		- 인터셉터는 **경로 정보**로 중복 호출 제거 (`excludePathPatterns("/error-page/**")`)
			- 인터셉터는 서블릿이 아니라 스프링이 제공하는 기능 -> `DispatcherType`과 무관
			- 따라서, **`excludePathPatterns`** 에 **오류 페이지 경로를 추가**하는 방식 사용

>자바 메인 메서드에서 Exception 발생 상황
>
>자바는 메인 메서드 직접 실행 시 `main()`이라는 이름의 쓰레드가 실행된다.
>만일, **`main()` 메서드 넘어 예외가 던져지면**, 예외 정보를 남기고 **해당 쓰레드가 종료된다.**

>스프링 부트 기본 예외 페이지 설정 끄는 방법 (`application.properties`)
>
>`server.error.whitelabel.enabled=false`

