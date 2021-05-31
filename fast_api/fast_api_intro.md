# Fast Api

## Fast API란?

파이썬으로 API를 만들기 위한 웹프레임워크입니다. 현재 파이썬 웹 프레임워크들 중 가장 빠른 속도를 보입니다.

* Node.js, Go와 대등할 정도의 높은 성능
* Automatic한 API Document를 지원 (Swagger UI, ReDoc에서 제공)
* pydantic을 활용해 type hint를 지원
* 에디터에서 지원되는 Auto Completion 기능이 훨씬 강력해짐

* Python 3.6 이상부터 지원



## Fast API with Open API Specification

Fast API에서 API를 정의할 때 사용하는 schema(어떠한 개념에 대한 추상적인 기술)는, Open API Specification을 따릅니다. Open API Specification은 Linux 재단에서 운영하는 공동 프로젝트로, API schema를 구상할 때 따르길 권장하는 표준입니다. 이 표준은 모든 HTTP API 스타일을 담진 않지만, 표준을 지켰을 때 RESTful API를 지향할 수 있도록 돕고 있습니다. 또한, 송수신하는 데이터 타입에 관해서는 JSON schema를 사용합니다.

Fast API는 Open API를 따르며 크게 2가지 이점을 얻습니다. 먼저, Open API schema는 Fast API에 포함된 2개의 Interactive API Documentation을 사용할 수 있게 합니다. 이외에 다른 Documentation을 외부에서 사용할 때도 Open API를 지키면 쉽게 Fast API에 가져와 이용할 수 있습니다. 다음으로 우리의 API로 소통하는 프론트엔드나 모바일, IoT 등의 클라이언트를 위한 코드를 자동으로 생성하기 위해 사용합니다.

​    

## 간단한 fast api 설치 및 앱 실행

* 원하는 가상 환경과 디렉토리에서 fast api 설치

  * `pip install fastapi`

* uvicorn 설치

  * `pip install uvicorn[standard]`

* main.py 파일 생성하기

  * 아래의 코드를 임시로 사용해서 진행하겠습니다.

    ```python
    from typing import Optional
    
    from fastapi import FastAPI
    
    app = FastAPI()
    
    
    @app.get("/")
    def read_root():
        return {"Hello": "World"}
    
    
    @app.get("/items/{item_id}")
    def read_item(item_id: int, q: Optional[str] = None):
        return {"item_id": item_id, "q": q}
    ```

* 서버 실행

  * `uvicorn main:app --reload`

* 서버를 실행하면 다음과 같은 api가 생성되어 사용할 수 있습니다.

  * http://127.0.0.1:8000 에서 api 접근합니다.
  * `/items/{item_id}` 로 접근 가능합니다. (GET method 적용)
  * 쿼리를 날려 JSON 형식으로 응답받을 수 있습니다.
    * 쿼리 요청: http://127.0.0.1:8000/items/5?q=somequery
    * 응답: `{"item_id": 5, "q": "somequery"}`
  * 다음 주소에서 Swagger UI에서 제공하는 Interactive API docs를 확인할 수 있습니다.
    * http://127.0.0.1:8000/docs 
  * 혹은 다음 주소에서 ReDoc에서 제공하는 또 다른 Interactive API docs를 확인할 수 있습니다.
