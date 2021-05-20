# Fast Api

## Fast API란?

파이썬으로 API를 만들기 위한 웹프레임워크입니다. 현재 파이썬 웹 프레임워크들 중 가장 빠른 속도를 보입니다.

* Node.js, Go와 대등할 정도의 높은 성능
* Automatic한 API Document를 지원 (Swagger UI, ReDoc에서 제공)
* pydantic을 활용해 type hint를 지원
* 에디터에서 지원되는 Auto Completion 기능이 훨씬 강력해짐

* Python 3.6 이상부터 지원



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
  
* 

