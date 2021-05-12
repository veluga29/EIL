# Fast Api

## 간단한 fast api 설치 및 앱 실행

* 원하는 가상 환경과 디렉토리에서 fast api 설치

  * `pip install fastapi`

* uvicorn 설치

  * `pip install uvicorn[standard]`

* 서버 실행

  * `uvicorn main:app --reload`

* 서버를 실행하면 다음과 같은 api가 생성되어 사용할 수 있습니다.

  * http://127.0.0.1:8000 에서 api 접근합니다.
  * `/items/{item_id}` 로 접근 가능합니다. (GET method 적용)
  * 쿼리를 날려 JSON 형식으로 응답받을 수 있습니다.
    * 쿼리 요청: http://127.0.0.1:8000/items/5?q=somequery
    * 응답: `{"item_id": 5, "q": "somequery"}`

  * 다음 주소에서 Swagger UI로 제공되는 Interactive API docs를 확인할 수 있습니다.
    * http://127.0.0.1:8000/docs 