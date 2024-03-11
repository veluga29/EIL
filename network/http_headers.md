## HTTP header 
- HTTP 전송에 필요한 모든 부가정보
- History
	- RFC2616 (폐기)
		- Header를 General header, Request header, Response header, Entity header로 분류
		- **Entity body**(실제 데이터)는 Message body에 담음
		- **Entity header**는 Entity body 해석을 위한 정보 제공 (`Content-Type`, `Content-Length`)
	- RFC723x
		- Entity => **Representation**(**표현**)
		- 회원이라는 리소스를 특정 데이터 형식(HTML, JSON, XML)으로 **표현**해 전달하겠다는 의미
		- Representation = **Representation Metadata** + **Representation Data**
		- Representation Data는 Payload(=Message body)에 담음

## 일반 HTTP 헤더
- 표현 헤더
	- `Content-Type`
		- 미디어 타입, 문자 인코딩
		- `text/html; charset=utf-8`, `application/json` (디폴트 인코딩: utf-8), `image/png`
	- `Content-Encoding`
		- 표현 데이터의 압축 정보 (전달자가 헤더 추가)
		- `gzip`, `deflate`, `identity`(=압축 X)
	- `Content-Language`
		- 자연 언어
		- `ko`, `en`, `en-US`
	- `Content-Length`
		- 바이트 단위
		- Transfer-Encoding 사용 시에는 필요 없음
- 협상 헤더 (Content Negotiation)
	- **클라이언트가 선호하는 표현을 서버에 요청**하고 서버는 최대한 클라이언트 선호에 맞춰 응답
	- 요청시에만 사용하는 헤더
	- 종류
		- `Accept` (미디어 타입)
		- `Accept-Charset` (문자 인코딩)
		- `Accept-Encoding` (압축 정보)
		- `Accept-Language` (자연 언어)
	- 협상 우선순위
		- Quality Values(q)
			- 0~1: 클수록 높은 우선순위
			- 생략 시 1
			- `Accpet-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7`
		- 구체적인 것이 우선
			- `Accept: text/*, text/plain, text/plain;format=flowed, */*`
			- `text/plain;format=flowed` > `text/plain` > `text/*` > `*/*`
- 전송방식 관련 헤더
	- 단순 전송
		- `Content-Length` 헤더와 함께 한번에 데이터 전송
	- 압축 전송
		- `Content-Length` + `Content-Encoding` 헤더와 함께 압축된 데이터를 전송
	- 분할 전송
		- `Transfer-Encdoing`: chunked 헤더와 함께 데이터를 일정한 단위로 쪼개어 보냄
		- `Content-Length` 헤더는 보내면 안됨
		- 큰 용량의 데이터를 한 번에 보내느라 기다리는 상황이 생기지 않도록, 분할된 데이터가 오는대로 바로바로 보여주는 방식
		- 서버에서 5byte가 만들어지면 클라이언트에 먼저 보내고, 또 만들어지면 또 보내서 마지막에 0바이트 `\r\n`을 보내고 끝을 표현
	- 범위 전송
		- `Range`(요청 헤더), `Content-Range`(응답 헤더)와 함께 범위를 지정해 데이터를 전송
		- 데이터를 절반정도 받다가 연결이 끊겼을 때, 못받은 범위만큼만 재요청하면 효율적
- 일반 정보 헤더
	- `From` (요청 헤더)
		- 유저 에이전트의 이메일 정보
		- 거의 사용되지 않지만 검색 엔진 같은 곳에서 주로 사용 (크롤링 그만해달라는 요청을 할 수 있는 연락 수단)
	- `Referer` (요청 헤더)
		- 이전 웹 페이지 주소
		- 유입 경로 분석에 사용
	- `User-Agent` (요청 헤더)
		- 클라이언트의 애플리케이션 정보 (웹브라우저 정보)
		- `user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/ 537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36`
		- 통계 정보 혹은 특정 브라우저의 장애에 대한 파악에 이용
	- `Server` (응답 헤더)
		- ORIGIN 서버의 소프트웨어 정보
		- ORIGIN 서버: 여러 프록시 서버, 캐시 서버를 제외하고 정말로 요청을 처리해 응답하는 서버
		- `Server: Apache/2.2.22 (Debian)`
		- `server: nginx`
	- `Date` (응답 헤더)
		- 메시지가 발생한 날짜와 시간
		- 최신 스펙에서 응답에만 사용하도록 명시
- 특별 정보 헤더
	- `Host` (요청 헤더, **필수**)
		- 요청한 호스트 정보 (도메인)
		- 클라이언트가 DNS를 거쳐 얻은 IP로 가상호스팅 중인 서버에 패킷을 보냈을 때, 어떤 도메인으로 전달해야 할지 판단하는 것에 구분점이 됨
		- 가상호스팅: 하나의 IP 주소에 여러 도메인이 적용되어 있는 상황 (도메인이 다른 여러 애플리케이션 구동)
	- `Location` (응답 헤더)
		- 페이지 리다이렉션
		- **201**: 요청에 의해 생성된 리소스 URI
		- **3xx**: 요청을 자동으로 리다이렉션할 리소스 URI
	- `Allow` (응답 헤더)
		- 해당 Path에서 허용 가능한 HTTP 메서드를 확인해 서버에서 보냄
		- **405** (Method Not Allowed)에는 반드시 포함
		- **실제로 구현되어 있는 곳은 별로 없음**
	- `Retry-After` (응답 헤더)
		- 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간
		- **503** (Service Unavailable) 응답 시 서비스가 언제까지 불능인지 알려줌
		- 날짜표기 혹은 초단위 표기
- 인증 헤더
	- `Authorization` (요청 헤더)
		- 클라이언트 인증 정보를 서버에 전달
		- `Authorization: Basic xxxxxxxxx`
		- `Authorization: Bearer xxxxxxxxx`
	- `WWW-Authenticate` (응답 헤더)
		- 리소스 접근시 필요한 인증 방법 정의
		- 정의해준 방법으로 다시 제대로 인증 정보를 생성해서 인증하라는 의미
		- **401** (Unauthorized)와 함께 사용
		- `WWW-Authenticate: Newauth realm="apps", type=1, title="Login to \"apps\"", Basic realm="simple"`
- 쿠키 헤더
	- 특징
		- HTTP는 Stateless 프로토콜이므로 **상태가 요구되는 상황**에서는 쿠키로 저장
			- 사용자 로그인 세션 관리
			- 광고 정보 트래킹
		- **GDPR**(General Data Protection Regulation, EU 개인정보보호 법령)로 인해 EU 회원국의 웹사이트들은 유저들로부터 **쿠키 수집 동의를 받아야 함** (필수쿠키, 기능쿠키, 성능쿠키, 마케팅쿠키 등에 대해 각각 선택도 가능)
		- 쿠키는 항상 서버에 전송되므로 **네트워크 트래픽**이 유발되기 때문에, **최소한의 정보**만 사용해야 함 (세션 id, 인증 토큰)
		- **보안에 민감한 데이터는 저장하면 안됨** (주민번호, 신용카드 번호)
		- 생명주기
			- 세션 쿠키: 만료 날짜가 생략된 쿠키는 브라우저 종료시까지만 유지
			- 영속 쿠키: 만료 날짜가 입력된 쿠키는 해당 날짜까지 유지
	- `Cookie` (요청 헤더)
		- 서버에서 받은 쿠키를 클라이언트가 HTTP 요청시 전달
	- `Set-Cookie` (응답 헤더)
		- 서버에서 클라이언트로 쿠키 전달
		- Field Value
			- `expires`
				- 만료일이 되면 쿠키 삭제
			- `max-age`
				- 0이나 음수를 지정하면 쿠키 삭제
			- `domain`
				- **쿠키를 전송받는** 서버 도메인의 범위 제한
				- 예시) `domain=example.com`
				- 명시
					- 기준 도메인 + 서브 도메인 적용
					- `example.com`&`dev.example.com`까지 쿠키 접근 가능(=쿠키 전송)
				- 생략
					- 기준 도메인만 적용
					- `example.com`만 쿠키 접근 가능
			- `path`
				- 해당 경로를 포함해 하위 경로 페이지까지만 쿠키 접근 가능
				- 일반적으로 `path=/` 루트로 지정
			- `Secure`
				- https인 경우에만 쿠키 전송
			- `HttpOnly`
				- 자바스크립트로 쿠키 접근 불가, http 전송에만 사용 가능
				- XSS 공격 방지
			- `SameSite`
				- **쿠키를 전송하는** 요청 도메인(=현재 접속해 있는 페이지)의 범위 제한
				- **요청 도메인**이 쿠키에 설정된 도메인과 같은 경우에만 쿠키 전송
				- XSRF 공격 방지
				- 속성
					- `Strict`: 같은 도메인에서만 접근 가능
						- 퍼스트 파티 쿠키 only
					- `Lax`: `<a>`, `<link>`, `<form method="GET">`통한 이동은 다른 도메인이어도 cookie 전송
						- Chrome 80 default
						- 퍼스트 파티 쿠키 + 일부 서드 파티 쿠키
					- `None`: cross-site에서도 쿠키 전송 가능 (단, **Secure 옵션 추가필수**)
						- 퍼스트 파티 쿠키 + 모든 서드 파티 쿠키

## 캐시와 조건부 요청 HTTP 헤더
- (캐시 제어 헤더) + (검증 & 조건부 요청 헤더 한 쌍) 캐시 조합 권장
	- `cache-control: max-age=...` + `Last-Modified`
	- `cache-control: max-age=...` + `ETag` (**Recommendation**)
- 캐시 기본 동작
	- 첫 번째 요청시응답에서 특정 캐시 헤더 및 바디 데이터를 **브라우저 캐시**에 저장
		- `cache-control: max-age=60`
		- `Last-Modified: 2023-04-23...`
		- `ETag: "aaaaaaaaa"`
	- 두 번째 요청시 **캐시 유효시간(`max-age` 값)** 검증
		- 유효: **캐시**에서 조회
		- 유효 X
			- **서버**로 요청
				- 조건부 요청 헤더 추가
					- 검증 헤더에 따라 `If-Modified-Since` 혹은 `If-None-Match`
				- 서버 검증
					- 기존 데이터 변경 X
						- `304 Not Modified` (HTTP Body X) 응답
						- **캐시에서 조회** (재사용)
						- 브라우저 캐시갱신 (응답 캐시 **헤더**)
					- 기존 데이터 변경
						- `200 OK`, 변경된 데이터 응답
						- 브라우저 캐시 갱신 (응답 캐시 **헤더 + 바디**)
- 헤더 종류
	- 캐시 제어 헤더
		- **Cache-Control** (캐시 제어)
			- `max-age`
				- 캐시 유효 시간, 초 단위
			- `no-cache`
				- 데이터를 캐시해도 되지만, 항상 원 서버(Origin Server)에 검증하고 사용
			- `no-store`
				- 데이터에 민감한 정보가 있으므로 저장하면 안됨 (메모리에서 사용하고 최대한 빨리 삭제)
		- Pragma (캐시 제어, HTTP 1.0 하위호환)
			- `no-cache` (위와 동일)
		- Expires (`Cache-Control: max-age` 하위호환, 함께 사용시 Expires는 무시됨)
			- 캐시 만료일을 정확한 날짜로 지정
	- 검증 헤더 (Validator)
		- **캐시 데이터와 서버 데이터가 같은지 검증**하는 데이터
		- `Last-Modified`
			- 데이터가 마지막으로 수정된 시간
			- 1초 미만 단위의 캐시 조정이 불가능
		- `ETag` (Entity Tag)
			- 캐시용 데이터에 임의의 고유한 버전 이름(Hash)을 붙이고 데이터 변경시 Hash 재생성
			- `ETag`가 같으면 캐시유지, 다르면 변경된 데이터 전송
			- **서버에서 별도 캐시 로직을 관리**하고 싶은 경우 사용
				- 데이터 수정 날짜가 다르지만 A -> B -> A로 수정해 데이터 결과가 똑같은 경우
				- 스페이스나 주석 같이 크게 영향 없는 변경 무시
				- 애플리케이션 배포 주기에 맞추어 `ETag` 모두 갱신
	- 조건부 요청 헤더
		- 검증 헤더를 통해 브라우저 캐시에 저장된 값으로 **조건에 따른 분기** 요청
		- **`If-Modified-Since`**: `Last-modified` 값 사용
		- `If-Unmodified-Since`: `Last-modified` 값 사용
		- **`If-None-Match`**: `ETag` 값 사용
		- `If-Match`: `ETag` 값 사용
- 장점
	- **비싼 네트워크 사용량을 줄일 수 있음** (캐시 유효시간동안 네트워크 이용은 용량이 적은 헤더 전달뿐)
	- **브라우저 로딩 속도가 매우 빨라져서 사용자 경험이 좋아짐**

## 프록시 캐시
- 원 서버가 멀리 있는 경우 중간에 프록시 캐시 서버(CDN 서비스)를 두어 **속도적 이점**을 얻음
	- 클라이언트(한국) - (0.5초) - 원 서버(미국)
	- 클라이언트(한국) - (0.1초) - 프록시 캐시 서버(한국 어딘가) - (0.4초) - 원 서버(미국)
- 보편적 캐시 방법
	- 첫 번째 접근이 오래걸리고 **두 번째 이후부터는 다운이 이미 받아져 빨라짐**
		- 유튜브의 인기 있는 영상은 로딩이 빠르고 인기 없는 영상은 로딩이 느림
	- 원 서버에서 캐시 서버로 데이터를 밀어 넣는 경우도 있음
- 관련 캐시 응답 헤더
	- `Cache-Control: public`
		- 응답이 public 캐시에 저장되어도 됨 (=중간 프록시 캐시 서버에 저장되어도 됨)
	- `Cache-Control: private`
		- 응답이 private 캐시에 저장되어야 함 (기본값)
	- `Cache-Control: s-maxage`
		- 프록시 캐시에 적용되는 max-age
	- `Cache-Control: must-revalidate`
		- 캐시 만료 후 최초 조회시 원 서버에 검증해야 함
		- 원 서버 접근 실패시 반드시 오류가 발생해야 함 (504 Gateway Timeout)
		- 캐시 시간이 유효하다면 캐시 사용
	- `Age: 60`
		- 원 서버에서 응답 후 프록시 캐시 내에 머문 시간(초)
- **확실한 캐시 무효화 응답**
	```
	Cache-Control: no-cache, no-store, must-revalidate
	Pragma: no-cache
	```
	- 기본적으로 웹브라우저 임의로 캐시를 할 수 있기 때문에 완전한 캐시 무효를 위해 사용
	- **네트워크 단절 등으로 인한 원 서버 접근 불가 시 `must-revalidate`이 필요**
		- `no-cache`의 경우 캐시 서버 설정에 따라 원 서버에 접근할 수 없는 경우, 캐시 데이터를 반환할 수 있음 (오류보다는 오래된 데이터라도 보여주기, 200 OK)
		- `must-revalidate`은 원서버에 접근할 수 없는 경우, **항상 오류 발생시킴** (**매우 중요한 돈과 관련된 결과들에 필수, 504 Gateway Timeout**)
- 용어
	- 원 서버 (Origin Server): 실제 요청을 처리하는 서버
	- public 캐시: 프록시 캐시 서버
	- private 캐시: 각각의 브라우저의 로컬 캐시

***
## Reference

[모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC)
[마케터를 위한 웹사이트 쿠키 동의 환경의 이해](https://osoma.kr/blog/cookie-consent/)
[What are the security differences between cookies with Domain vs SameSite strict?](https://stackoverflow.com/questions/57090774/what-are-the-security-differences-between-cookies-with-domain-vs-samesite-strict)
[SameSite란? None, Lax, Strict](https://jjam89.tistory.com/231)s