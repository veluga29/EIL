# REST API 이해하기

## REST API의 정의

* Representational State Transfer의 약자
* 정보들을 주고 받는 HTTP 요청을 보낼 때, 어떤 URI에 어떤 메서드를 사용할지 개발자들 사이에 널리 지켜지는 약속 (Software Architecture)
* REST를 지켰을 때 각 요청이 어떤 동작이나 정보를 위한 것인지를 그 요청의 모습 자체만 봐도 추론 가능해짐
* 과거의 복잡했던 SOAP 방식을 대체하여 최근에 가장 널리 쓰이는 양식

​    

## REST API의 구성

* 자원(Resource) - URI를 통해 식별 (네트워크 상에 존재하는 자원을 구분하는 식별자)
* 행위(Verb) - HTTP Method에 따라 자원에 접근
* 표현(Representations) 혹은 정보(Message) - HTTP 헤더와 바디, 응답 코드를 활용

​    

## REST의 특징

* Uniform
  * 리소스에 대한 조작이 통일되고 한정적인 인터페이스로 구성된 아키텍처 스타일 (Uniform Interface)

* Stateless
  * 작업을 위한 상태정보를 따로 저장, 관리하지 않고 단순히 들어오는 요청만 처리
  * 덕분에 구현이 단순해짐
* Cacheable
  * 기존 웹표준을 사용하므로 웹의 기존 인프라를 이용해 캐싱 기능 적용 가능
* Self-descriptiveness
  * REST API 메시지만으로도 무슨 의미인지 쉽게 이해할 수 있는 자체 표현 구조를 가짐
* Client - Server 구조
  * 서버와 클라이언트의 구분이 명확
* 계층형 구조
  * REST 서버는 다중 계층으로 구성될 수 있으며 보안, 로드 밸런싱, 암호화 계층을 추가해 구조상의 유연성을 둘 수 있음
  * PROXY, 게이트웨이 같은 네트워크 기반의 중간매체를 사용할 수 있게 함

​    

## REST API 디자인 가이드 요약

* REST API 중심 규칙

1. URI는 정보의 자원을 표현해야 합니다.
   * 리소스 명은 동사보다는 명사를 사용
2. 자원에 대한 행위는 HTTP Method(GET, POST, PUT, DELETE)로 표현합니다.
   * POST: 해당 URI를 요청하면 리소스를 생성합니다.
   * GET: 해당 리소스를 조회합니다.
   * PUT: 해당 리소스를 수정합니다.
   * DELETE: 해당 리소스를 삭제합니다.

* URI 설계 시 주의할 점
  * 슬래시 구분자는 계층관계를 나타낼 때 사용합니다.
  * 마지막 문자로 슬래시를 포함하지 않습니다.
  * 하이픈(-)은 URI 가독성을 높이는데 사용합니다.
  * 언더스코어(_)는 가독성을 해치므로 URI에 사용하지 않습니다.
  * URI 경로에는 소문자가 적합합니다.
  * 파일 확장자(.jpg, .png 등)는 URI에 포함시키지 않습니다.
* 리소스 간의 관계를 표현하는 방법
  * REST 간의 연관 관계는 다음과 같이 표현합니다.
    * `/리소스명/리소스 ID/관계가 있는 다른 리소스명`
    * `GET : /users/{userid}/books` (일반적으로 소유 ‘has’의 관계를 표현할 때)
  * 관계명이 복잡하다면 서브 리소스에 명시적으로 포함할 수 있습니다.
    * `GET : /users/{userid}/likes/books` (관계명이 애매하거나 구체적 표현이 필요할 때)
* Collection과 Document 개념을 활용한 리소스 표현
  * Collection: 문서들의 집합, 객체들의 집합
  * Document: 하나의 문서, 하나의 객체
  * Collection과 Document로 표현하면 URI 설계가 더욱 용이해집니다.
    * `http://restapi.com/sports/soccer/players/7`
    * sports, players라는 Collection과 soccer, 7이라는 document로 표현
  * Collection은 복수로 Document는 단수로 표현해주는 것이 좋습니다.

​     

## REST API의 정보

### HTTP 바디

자원에 대한 정보를 HTTP 바디에 데이터로 담아 전달합니다. 데이터 포멧으로는 최근 JSON이 가장 많이 쓰입니다.

### HTTP 헤더   

HTTP 바디의 컨텐츠 종류를 명시할 수 있고 인증 권한 정보를 담습니다. 요청 HTTP 헤더는 'Accept' 항목을, 응답 HTTP 헤더는 'Content-type'을 담습니다. 다음은 'Content-type'의 몇 가지 예입니다.

- application/json
- application/xml
- text/plain
- image/jpeg
- image/png

### HTTP 응답 상태 코드

잘 설계된 REST API는 URI 뿐만 아니라 요청에 대한 응답까지 잘 내어주어야 합니다.

* 200: 클라이언트의 요청을 정확히 수행함
* 201: 클라이언트가 어떤 리소스 생성을 요청했고, 해당 리소스가 성공적으로 생성됨 (POST로 리소스 생성 시)
* 400: 클라이언트의 요청이 부적절함
* 401: 인증 받지 않은 클라이언트가 인증이 필요한 리소스를 요청함
* 403: 인증 유무와 관계없이, 응답하고 싶지 않은 리소스를 클라이언트가 요청했을 때 사용
  * 리소스의 존재를 인정하는 것이므로 403 사용을 지양하고 401, 404 사용을 권고
* 404: 클라이언트가 요청하는 리소스를 찾을 수 없음
* 405: 클라이언트가 요청한 리소스에서는 사용 불가능한 메서드를 이용함
* 301: 클라이언트가 요청한 리소스에 대한 URI가 변경됨
  * Location header에 변경된 URI 적어줄 것
* 500: 서버에 문제가 있음

​    

## Reference

[REST API 제대로 알고 사용하기](https://meetup.toast.com/posts/92)

[REST API 이해하기](https://blog.hjf.pe.kr/462)



