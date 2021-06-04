# Fast API 튜토리얼 - Parameters of Path, Query, Request body

## Path Parameters

### Path Parameters의 정의와 형태

Path parameter는 path 내에 들어있는 variable의 value를 전달받은 parameter를 말합니다.

```python
@app.get("/items/{item_id}")
def read_item(item_id):
    return {"item_id": item_id}
```

위의 코드에서, `item_id`는 path parameter에 해당합니다. HTTP 요청이 들어오면 해당 URL에서 `{item_id}`에 해당하는 value를 획득하고, 이 value는 `read_item`함수의 `item_id`에 인자로 전달됩니다.

위의 코드를 main.py에 추가해 저장한 후, http://127.0.0.1:8000/items/foo에 들어가면 response로 `{"item_id":"foo"}`이 확인됩니다.

### Data conversion and validation

```python
@app.get("/items/{item_id}")
def read_item(item_id: int):
    return {"item_id": item_id}
```

또한, path operation function에서 인자로 사용한 path parameter에 타입 힌트를 줄 수 있습니다. (다른 parameter도 마찬가지로 적용됩니다.) 그리고 이렇게 자료형을 annotate한 parameter는 **들어온 인자 값을 annotated된 자료형대로 형 변환**해서 parameter에 담습니다. 만일  http://127.0.0.1:8000/items/3으로 요청이 들어온 경우, 원래는 path parameter를 `str` 타입으로 받아 `item_id` 값이 '3'이 되지만 위 코드에서는 타입 힌트를 보고 `int`로 형 변환된 3이 담깁니다. 즉, Fast API는 타입 힌트를 통해 자동으로 parsing을 통한 data conversion을 제공합니다.

만일 path parameter에 annotated된 타입과 다른 타입의 값이 요청된다면, 해당 HTTP 요청은 에러를 일으킵니다. 이는 Fast API가 **데이터 유효성 검사까지 수행함**을 보여줍니다. 실제로 http://127.0.0.1:8000/items/foo에 들어가면 응답에 오류가 발생합니다. Annotated된 `int` 타입으로 형 변환이 이뤄질 수 없는 `foo`가 값으로 들어왔기 때문입니다. http://127.0.0.1:8000/items/4.2의 경우도 마찬가지입니다.

타입 힌트로 annotated된 변수는 Interactive API documentation에도 적용됩니다. http://127.0.0.1:8000/docs에 들어가면 path parameter `item_id`가 integer로 선언되어 있음을 확인할 수 있습니다.

Fast API에서 이러한 data conversion 및 validation이 가능한 이유는 내부적으로 Pydantic 라이브러리의 도움 덕분입니다.

​    

> Pydantic이란?
>
> 파이썬 타입 힌트를 사용해 데이터 유효성 검사를 해주는 라이브러리입니다. 만일 어노테이션된 타입과 다른 데이터를 만나면 에러를 띄웁니다. Fast API에서는 Pydantic을 활용하여 간편하게 데이터 유효성 검사를 수행합니다.

​    

### Path Operation 정의 순서의 중요성

어떤 path operation들은 정의하는 순서에 따라 예상치 못한 처리를 일으킬 수 있습니다. 예를 들어, 고정된 path를 가진 path operation과 path parameter를 가진 path operation이 모두 정의된 경우를 살펴봅시다.

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/users/me")
async def read_user_me():
    return {"user_id": "the current user"}


@app.get("/users/{user_id}")
async def read_user(user_id: str):
    return {"user_id": user_id}
```

`/users/me` 코드는 `/users/{user_id}`보다 앞에 쓰여져야 합니다. 만일 순서가 바뀌면, Fast API는 `me`를 `user_id`의 value로 오해하여 본래 의도와 다르게 `read_user` 함수를 호출할 것입니다.

​    

### Path Parameter의 값으로 Path를 받는 경우

때로는 path parameter의 값으로 `home/dogs/wealsh`와 같은 path가 올 수 있습니다. 만일 path operation의 path가 기존처럼 `/files/{file_path}`이라면, `file_path`는 `/files/home/dogs/wealsh` 요청이 들어왔을 때 이를 온전히 인식하지 못하고 `{"detail":"Not Found"}`를 응답합니다. 하지만, Starlette에서 제공하는 Path convertor를 사용하면 path parameter의 인자가 path 형태로 들어와도 이를 온전히 인식하게 됩니다. Fast API는 Starlette을 기반으로 만들어졌기 때문에, 특별한 import 없이 다음과 같이 써주면 path convertor가 동작합니다.

```python
/files/{file_path:path}
```

이를 활용하면 다음과 같이 path operation에 http://127.0.0.1:8000/files/home/dogs/wealsh 형태로 요청을 보내도 온전히 동작합니다. 

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/files/{file_path:path}")

async def read_file(file_path: str):
    return {"file_path": file_path}
```

위의 요청의 경우 `files/home/dogs/wealsh` 값이 `file_path`에 담겨 응답됩니다. 만일 `/files/home/dogs/wealsh` 형태로 앞에 `/`를 추가하여 `file_path`에 담고 싶다면 http://127.0.0.1:8000/files//home/dogs/wealsh 형태로 요청을 보내면 됩니다.

​    

## Query Parameters

### Query Parameters의 정의와 형태

Path operation function에 path parameter가 아닌 다른 parameter를 선언했다면, 해당 parameter들은 자동으로 query parameter로 인식됩니다. Query parameter는 request로 들어오는 query의 값이 담기는 parameter입니다.

Query는 URL의 `?`뒤에 오는 key-value pair를 의미하며 각각의 query는 `&`로 구분됩니다. 다음은 request에 담긴 query의 예시입니다.

```
http://127.0.0.1:8000/items/?skip=0&limit=10
```

또한, 다음과 같은 path operation은 이러한 request에 대해 query parameter를 받습니다.

```python
from fastapi import FastAPI

app = FastAPI()

fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]


@app.get("/items/")
async def read_item(skip: int = 0, limit: int = 10):
    return fake_items_db[skip : skip + limit]
```

이 경우, query parameter는 `skip`과 `limit`이고 각각 0과 10을 인자로 받습니다.

원래대로라면 URL로부터 들어온 `str`타입의 '0'과 '10'으로 값을 받았겠지만, `skip`과 `limit`의 타입을 `int`로 선언했기 때문에 형 변환하여 값을 받습니다. 즉, query parameter에도 path parameter에서 적용되던 다음과 같은 프로세스들이 그대로 적용됩니다.

* Editor Support (Auto completion, Error check, etc...)
* Data conversion
* Data validation
* Automatic Documentation

​    

### Default value & Optional Parameters

```python
from fastapi import FastAPI

app = FastAPI()

fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]


@app.get("/items/")
async def read_item(skip: int = 0, limit: int = 10):
    return fake_items_db[skip : skip + limit]
```

Query parameter는 default parameter를 설정할 수 있습니다. 이 경우 `skip`과 `limit`의 default 값은 각각 0과 10입니다.

```python
from typing import Optional

from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_item(item_id: str, q: Optional[str] = None):
    if q:
        return {"item_id": item_id, "q": q}
    return {"item_id": item_id}
```

또한, query parameter에는 typing 모듈을 활용해서 Optional 타입을 선언할 수 있습니다. `q: Optional[str] = None`은 query parameter `q`가 `str` 타입의 value를 인자로 받거나 혹은 인자가 없을 때는 `None`을 default value로 가진다는 의미입니다. 즉, Fast API는 `q`를 required하지 않은 parameter로 인식합니다.

이 때, Fast API는 `= None`부분을 인식해 query parameter `q`의 required 여부를 구분합니다. 또한, `: Optional[str]` 부분에서 Fast API는 `str` 부분만 인식해 data conversion 및 data validation에 사용합니다. 그리고 나머지 `Optional` 부분은 Fast API가 아닌 Editor의 Auto completion과 Error check를 support하기 위해 사용됩니다. 

​    

> Required parameter란?
>
> Parameter가 Required하다는 것은 특정 parameter가 필수적으로 인자를 받아야만 함을 말합니다. 보통 특정 parameter에 default값을 설정해두면 not required, default 값을 설정하지 않으면 required 상태로 인식됩니다. 만일 not required한 parameter를 굳이 특정 값이 있지 않아도 되는 Optional parameter로 만들고 싶다면, default 값으로 `None`을 설정하면 됩니다.

​    

## Request Body

### Request Body의 정의와 형태

Request body는 클라이언트에서 API로 보내는 data를 의미합니다. 반면에, API가 클라이언트에게 보내는 data는 response body라고 합니다. Response body는 API가 항상 보내야 하는 반면, request body는 클라이언트가 필수적으로 보낼 필요는 없습니다.

```python
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None

        
app = FastAPI()


@app.post("/items/")
async def create_item(item: Item):
    return item
```

Request body는 Pydantic model을 통해 선언합니다. `pydantic` 라이브러리에서 `BaseModel`을 import하고, `BaseModel`을 상속하는 클래스를 생성해 Pydantic 모델을 만듭니다. Model의 attribute들은 query parameter와 같은 방식으로 required 여부를 정할 수 있습니다. 위 경우, `name`,  `price`는 required한 attribute이고 `description`, `tax`는 not required하면서 optional한 attribute입니다.

따라서, 위 모델은 다음과 같은 JSON 객체(혹은 Python dict 객체)를 선언한 것과 같습니다.

```json
{
    "name": "Foo",
    "description": "An optional description",
    "price": 45.2,
    "tax": 3.5
}
```

`description`과 `tax`는 optional하기 때문에 다음과 같은 JSON 객체도 request body로 유효하게 전달 받을 수 있습니다.

```json
{
    "name": "Foo",
    "description": "An optional description",
    "price": 45.2,
    "tax": 3.5
}
```

그리고 path operation fucntion의 parameter에 원하는 pydantic model을 타입 선언 해주면, 해당 파라미터는 request body를 전달받는 parameter로 인식됩니다. 위 코드에서는 `async def create_item(item: Item):`에서 `Item` pydantic model을 타입으로 선언해 `item`을 request body parameter로 만들었습니다.

이렇게 선언된 request body parameter는 다음과 같은 특징을 가집니다.

* Request body로 들어온 data를 JSON 형식으로 읽어들입니다.
* 필요할 경우 들어온 data를 선언된 타입에 일치하도록 data conversion합니다.
* 선언된 타입으로 Data validation을 수행합니다. (Incorrect data에는 error를 띄웁니다!)
* Editor support를 지원합니다.
* 해당 model에 대한 JSON schema를 생성해, Automatic Documentation에 적용합니다.

​     

### Request Body로 전달받은 Model 사용법

```python
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


app = FastAPI()


@app.post("/items/")
async def create_item(item: Item):
    item_dict = item.dict()
    if item.tax:
        price_with_tax = item.price + item.tax
        item_dict.update({"price_with_tax": price_with_tax})
    return item_dict
```

Request body를 전달받은 `item`은 클래스의 attribute를 사용하는 것과 똑같은 방식으로 자유롭게 사용할 수 있습니다. 예를 들어, `item.tax`처럼 `tax` 속성에 접근해 value를 사용할 수 있습니다. 또한, pydantic model의 `.dict()` 메서드를 사용해 `item.dict()`로 해당 model의 데이터를 python `dict` 형태로 사용할 수도 있습니다.

위 코드는 `tax` 속성에 인자가 들어왔다면, `price_with_tax = item.price + item.tax`로 새로운 value를 만들고 `item`에서 추출한 `item_dict`에 `item_dict.update({"price_with_tax": price_with_tax})`로 새로운 key-value를 추가하여 `item_dict`를 return합니다.

​    

## Path + Query + Request Body Parameters

Path, query, request body parameter는 모두 동시에 사용할 수 있습니다. Fast API는 각각의 parameters를 자동으로 구분해냅니다

```python
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


app = FastAPI()


@app.put("/items/{item_id}")
async def create_item(item_id: int, item: Item, q: Optional[str] = None):
    result = {"item_id": item_id, **item.dict()}
    if q:
        result.update({"q": q})
    return result
```

위 경우 `item_id`는 path parameter, `item`은 request body parameter, `q`는 query parameter로 자동 인식됩니다. 기본적으로 parameter 자동 인식은 다음과 같은 기준으로 진행됩니다.

* Path 안에도 선언되어 있는 parameter는 path parameter로 인식합니다. (혹은 `Path(...)`가 선언되어 있는 parameter)
* `int`, `float`, `str`, `bool` 등의 singular type으로 선언된 parameter는 query parameter로 인식합니다. (혹은 `Query(...)`가 선언되어 있는 parameter)
* Pydantic model로 type이 선언된 parameter는 request body parameter로 인식합니다. (혹은 `Body(...)`가 선언되어 있는 parameter)

​    

## Path, Query, Request body Parameters의 순서 문제

Query parameter를 default 값이 없는 required parameter로 만들고, path parameter는 default 값으로 Path 인스턴스를 넣어 not required한 parameter로 만드는 다음과 같은 상황을 가정해보겠습니다.

```python
async def read_items(
    item_id: int = Path(..., title="The ID of the item to get"), q: str
):
```

이 때, Python 문법으로 인해 default 값이 있는 parameter는 default 값이 없는 parameter의 앞에 위치하지 못합니다. 따라서, 위 코드는 오류를 일으킵니다.

하지만, 다음과 같이 순서를 정리하면 오류를 피할 수 있습니다.

```python
async def read_items(
    q: str, item_id: int = Path(..., title="The ID of the item to get")
):
```

Fast API는 parameter의 이름, 타입, default parameter 등의 단서를 통해 parameter의 종류를 인식하므로, 순서에 대한 문제는 Python 문법에서만 고려하면 됩니다.

만일 다음과 같은 약간의 트릭을 사용한다면, default 값 여부에 상관 없이 자유로운 parameter 배열이 가능합니다.

```python
async def read_items(
    *, item_id: int = Path(..., title="The ID of the item to get"), q: str
):
```

`*`를 함수의 첫 번째 parameter로 사용하면 위와 같이 default 값이 없는 parameter가 뒷 순서로 와도 상관 없습니다. `*`는 Python 함수의 special parameter 중 하나로, `*` 뒤에 위치한 parameter들은 모두 키워드 인자만 받도록 강제합니다. Special parameter에 대해 더 자세히 알고 싶다면, Python 공식 튜토리얼 문서의 [Special parameters](https://docs.python.org/3/tutorial/controlflow.html#special-parameters) 부분을 읽어 보시길 바랍니다.

​    

## Reference

[Fast API 공식 문서 튜토리얼](https://fastapi.tiangolo.com/tutorial/)
