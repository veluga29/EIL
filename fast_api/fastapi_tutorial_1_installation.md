# Fast API 튜토리얼 - Installation

Fast API 공식 문서의 튜토리얼을 살펴보고 정리합니다. 본 글은 윈도우 환경을 기준으로 작성되었습니다.

​    

## Fast API 설치하기

앞에서는 간단히 Fast API와 uvicorn만 설치하여 진행했지만, 이번엔 튜토리얼을 편하게 진행하기 위해 Fast API와 이에 따른 의존 관계가 있는 모듈들을 한꺼번에 설치하겠습니다. 

가상환경을 사용한다면 활성화시켜주시고, [all] 옵션을 사용해 Fast API와 관련 모듈들을 한번에 설치합니다. 

```powershell
> pip install fastapi[all]
```

이 때 uvicorn 서버도 함께 설치되기 때문에, 따로 uvicorn을 설치할 필요없이 프로젝트 디렉토리에 main.py 파일만 만들고 바로 서버를 구동할 수 있습니다.

가장 simplest한 형태의 Fast API 코드를 main.py에 작성해 실행해봅시다. main.py 파일을 프로젝트 폴더에 생성하고 다음 코드를 입력해 저장합니다.

```python
# main.py
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def root():
    return {"message": "Hello World"}
```

>**코드 분석**
>
>`from fastapi import FastAPI`: fastapi 모듈에서 파이썬 클래스 FastAPI를 import합니다.
>
>`app = FastAPI()`: app 변수에 FastAPI 인스턴스를 만들어 담습니다. 해당 변수에 이름에 따라 바로 이어 나올 uvicorn 명령어를 다르게 사용합니다. `uvicorn main:[변수이름] --reload`처럼 말이죠!
>
>`@app.get("/")`: path operation decorator를 만듭니다. path란 `//`를 제외하고 url에서 첫 번째로 만나는 `/`로부터 시작되는 url의 뒷 부분을 말하며, operation은 HTTP method를 말합니다. 이 코드의 경우, decorator가 장식하는 함수가 'GET operation을 사용해 `/` path로 가라는 요청'을 처리하는 역할을 한다고 FastAPI에게 알려줍니다.
>
>`def root():`: path operation function을 정의합니다. 이 코드의 경우, FastAPI는 GET operation으로 URL `/`에 대한 요청이 들어오면 이 함수를 호출합니다.
>
>`return {"message": "Hello World"}`: content를 리턴합니다. 리턴할 수 있는 객체는 `dict`, `list`, `int`, `str`, Pydantic model 등 다양합니다.

​    

그리고 Uvicorn 서버를 구동합니다.

```powershell
> uvicorn main:app --reload
```

​    

>**`uvicorn main:app --reload`의 의미**
>
>* `uvicorn`: uvicorn 서버를 실행합니다
>* `main`: main.py 파일(모듈)을 의미합니다.
>* `app`: main.py 내에서 생성한 FastAPI 클래스의 객체를 의미합니다.
>* `--reload`: 코드를 수정한 후 자동으로 서버를 재시작해주는 옵션입니다. 현재 개발 중일 때 사용합니다.

​    

이제 브라우저로 로컬머신에서 작동 중인 앱을 확인해봅시다. http://127.0.0.1:8000 주소에 들어가면, JSON 형태의 응답으로 다음과 같이 새로이 마주하는 Fast API 세상과 인사를 나눌 수 있습니다!

<img src="../image/fast_api_img/hello_world.JPG" alt="hello world" style="zoom:45%;" />

> Uvicorn이란?
>
> ![uvicorn](../image/fast_api_img/uvicorn.jpg)
>
> uvloop와 httptools를 사용하는 초고속 ASGI(*Asynchronous Server Gateway Interface*) web server입니다. 최근까지 파이썬은 asyncio 프레임 워크를 위한 저수준 서버 / 애플리케이션 인터페이스가 없었는데, uvicorn의 등장으로 Fast API같은 프레임워크의 비동기 처리 성능이 크게 향상됐습니다.

> Starlette이란?
>
> <img src="../image/fast_api_img/starlette.png" alt="starlette" style="zoom:50%;" />
>
> Uvicorn 위에서 실행되는 비동기적으로 실행할 수 있는 web application server입니다. FastAPI는 Starlette 위에서 동작하고, Starlette 클래스를 상속받았기 때문에, Starlette의 모든 기능을 사용할 수 있습니다.

​    

## Reference

[Fast API 공식 문서 튜토리얼](https://fastapi.tiangolo.com/tutorial/)

[Uvicorn이란?](https://chacha95.github.io/2021-01-16-python6/)

[비동기 Micro API server로 좋은 FastAPI](https://chacha95.github.io/2021-01-17-python6.5/)