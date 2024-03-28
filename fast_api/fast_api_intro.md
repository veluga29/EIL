## FastAPI
- **마이크로 프레임워크**
	- 마이크로 프레임워크: 필수 기능만 제공하는 경량화된 프레임워크
	- **꼭 필요한 기능만 사용해 빠르고 가볍게 개발 가능**
	- 풀스택 프레임워크(장고)가 항공 모함이라고 한다면, 마이크로 프레임워크는 돛단배
	- 추가적으로 필요한 기능은 라이브러리를 붙여 해결
- **빠른 속도**
	- **비동기 프로그래밍**으로 **I/O 작업(DB 쿼리, 네트워크 통신)이 많은 애플리케이션에 높은 성능**
	- 즉, 한 요청이 끝나야 다음 요청을 처리(동기 처리)하는 Django, Flask와 달리, 한 요청이 끝나기 전에 다른 요청을 함께 처리 가능 (비동기 처리)
	- **Node.js, Go와 대등할 정도의 높은 성능** (**Starlette**을 상속 받아 강화시킴)
- **생산성**
	- **간결한 코드**로 쉽게 API 개발
	- **빠른 러닝 커브**
- 자동 문서화
	- **Swagger UI**, **ReDoc**
- 유효성 검사
	- **Pydantic**과의 integration으로 강력한 데이터 유효성 검사 (type hint로 지원)
- 강력한 IDE Auto Completion
## FastAPI with Open API Specification
- Open API Specification
	- **API schema를 구상할 때 따르길 권장하는 표준**
	- Linux 재단에서 운영하는 공동 프로젝트
	- 모든 HTTP API 스타일을 담진 않지만, 표준을 지켰을 때 RESTful API를 지향할 수 있도록 도움
	- 송수신하는 데이터 타입: **JSON schema**
- FastAPI & Open API Specification
	- **FastAPI는 Open API Specification을 따름**
	- Open API schema를 통해 Swagger UI와 ReDoc 문서 자동 생성
## Starlette
- FastAPI가 상속하고 있는 경량화된 ASGI 프레임워크
- **파이썬 프레임워크 중 가장 빠르며** 고성능 async 서비스를 만들기 적합
- FastAPI는 속도가 빠른 Starlette에 몇가지 기능을 추가한 것
	- 타입 힌트를 통한 데이터 유효성 검사
	- 직렬화
	- 문서 자동화
	- 의존성 주입 및 보안 유틸리티 등
## Uvicorn
- **uvloop**과 httptools를 사용하는 **초고속 ASGI 서버**
- FastAPI와 Starlette은 Uvicorn 서버 위에서 동작
- FastAPI와 Starlette의 속도가 빠른 이유
## Pydantic
- 파이썬 타입 힌트를 사용하여 **데이터 유효성 검사**를 수행하는 라이브러리
- 빠르고 확장성이 높음
- IDE 지원이 잘됨
- **FastAPI에서는 데이터 유효성 검사와 더불어 직렬화 및 API 문서 정의에 사용**