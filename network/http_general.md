## 기본 용어
- IP (Internet Protocol)
	- **패킷(Packet)을 단위로 특정 주소(IP Address)에 데이터를 전달할 수 있는 프로토콜**
	- IP 패킷 (보내려는 메시지 + 출발지 IP, 도착지 IP...)
	- 한계
		- **비연결성**
			- 패킷을 받을 대상이 없거나 상대 서버가 불능 상태여도 전송
		- **비신뢰성**
			- 중간에 패킷이 누락되거나 순서대로 오지 않는 경우 존재
		- **프로그램 구분**
			- 같은 IP인데 통신하는 애플리케이션이 2개 이상인 경우 구분 불가
- 전송계층(Transport Layer)
	- 네트워크 4계층에서 TCP 혹은 UDP 추가 정보로 IP 패킷을 보완하는 단계
- TCP (Transmission Control Protocol)
	- **앞선 IP의 문제점을 해결 (전송제어 정보를 패킷에 추가)**
	- TCP/IP 패킷 (IP 패킷 + 출발지 PORT, 목적지 PORT, 전송제어, 순서, 검증정보...)
	- 특징
		- **연결지향 (3 way handshake)**
			- SYN, SYN+ACK, ACK 3단계로 연결을 확인하고 그 후 데이터를 보냄
			- 최근엔 최적화되어 세 번째 단계 ACK에서 데이터를 함께 보내는 것이 가능
		- 데이터 전달 보증
			- 서버는 데이터를 잘 받았다는 응답을 클라이언트에게 줌
		- 순서 보장
			- 기본적으로는 패킷 1, 3, 2 순서로 왔다면 2부터 다시 보낼 것을 클라이언트에 요청
			- 서버 최적화에 따라 다시 보내달라는 요청 없이 내부적으로 처리하기도 할 것
- UDP (User Datagram Protocol)
	- IP와 비슷할 정도로 기능이 거의 없음 (하얀 도화지)
		- **PORT, 체크섬 정도만 추가**
		- TCP의 연결지향, 데이터 전달 보증, 순서 보장 등이 없다.
	- **덕분에 단순하고 빠름**
		- TCP는 3 way handshake와 패킷의 추가정보들로 인해 데이터가 크고 속도가 느림
		- 따라서, 속도 최적화는 UDP 이용
		- HTTP3 스펙에서도 UDP를 활용하며 최근 각광
- PORT
	- 같은 IP(내 서버) 내에 여러 프로세스가 통신 중일 때, 응답 패킷이 어느 애플리케이션의 패킷인지 구분
	- IP가 아파트면 PORT는 동호수를 표현
	- 0~65535 할당 가능
	- 0~1023은 잘 알려진 포트로 사용하지 않는 것이 좋음
		- HTTP - 80
		- HTTPS - 443
- DNS (Domain Name System)
	- 전화번호부 같은 서버를 제공하여 도메인명을 IP 주소로 변환하는 역할 수행
	- IP는 기억하기 어렵고 가변적이어서 DNS가 이를 해결
- URI (Uniform Resource Identifier)
	- 자원을 식별하는 방법을 총칭
	- URL(Uniform Resource Locator) + URN(Uniform Resource Name)
		- URL: `https://www.inflearn.com/course/lecture`
		- URN: `urn:isbn:01270712`
	- URN은 보편화 되지 않아서 **URI = URL로 생각해도 무방하다.**
## URL 문법
- Syntax: **scheme://\[userinfo@]host\[:port]\[/path]\[?query]\[#fragment]**
- 예시: https://www.google.com:443/search?q=hello&hl=ko
- `scheme`
	- 주로 **프로토콜** 사용 (어떤 방식으로 자원에 접근할 것인가에 대한 약속)
	- `http`, `https`, `ftp`
- `port`
	- `http` 80 포트, `https` 443 포트 등 보편적인 경우 **생략 가능**
- `userinfo`
	- URL에 사용자 정보를 포함해서 인증하는 경우 사용하지만 **거의 쓰이지 않음**
- `host`
	- **도메인명** 또는 **IP 주소**를 직접 사용 가능
- `path`
	- 계층적 구조의 리소스 경로
- `query`
	- key-value 형태
	- `?`로 시작, `&`로 추가
	- 서버로 요청시 모두 **문자로 넘어감**
	- = `query parameter` = `query string`
- `fragment`
	- `html` **내부 북마크**에 사용
	- 서버 전송 정보가 아님

