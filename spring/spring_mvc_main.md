## Web Server & Web Application Server
- Web Server (HTTP)
	- **정적 리소스 제공** + 기타 부가기능
	- 동적인 처리(애플리케이션 로직 등)가 필요하면 **WAS에 요청 위임**
	- 예시) NGINX, APACHE
- Web Application Server (HTTP)
	- **애플리케이션 로직 수행** (프로그램 코드 실행) + 웹 서버 기능 (정적 리소스 제공)
		- 동적 HTML, HTTP API(JSON), 서블릿, JSP, Spring MVC
	- API 서버만 제공할 경우 WAS만으로 서버 구축해도 괜찮음 (회사끼리 데이터 주고 받을 때)
	- 예시) Tomcat, Jetty, Undertow
- WAS는 애플리케이션 코드 실행에 더 **특화**되어 있다!
	- 웹서버와 WAS는 서로가 서로의 기능을 가지고 있긴 해서 경계가 모호
	- 서블릿 컨테이너 기능 제공하면 WAS라 보기도 함 (서블릿 사용안하는 프레임워크도 있지만...)
- 공존 이유
	- 효율적인 리소스 관리
		- WAS가 너무 많은 역할을 담당하여 **서버 과부하 우려**
		- **애플리케이션 로직은 값어치가 높으므로** 값이 낮은 정적 리소스 때문에 과부하되면 안됨
		- 역할 분리
			- 정적 리소스 사용이 많을 때는 Web Server 증설
			- 애플리케이션 리소스 사용이 많을 때는 WAS 증설
	- 지속적인 오류 화면 제공
		- WAS는 잘 죽는 반면, Web Server는 잘 안 죽음
		- WAS 및 DB 장애시 **Web Server가 오류화면 제공 가능**

## Servlet
