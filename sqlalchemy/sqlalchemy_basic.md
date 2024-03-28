## SQLAlchemy
- 동기 지원 모듈: `sqlalchemy`
	- `create_engine` (데이터베이스 엔진)
	- `Session` (세션)
	- `sessionmaker` (세션 팩토리)
- ORM Setting 기본 단계
	- DB engine 생성 및 접속
	- 세션 정의 및 생성
	- 테이블 초기 생성
##  Session을 만드는 2가지 방법
- `Session` 객체를 직접 생성
	- 사용 코드
	```python
	def get_db():
	    db = Session(bind=engine)
	    try:
	        yield db
	    finally:
	        db.close()
	```
	- FastAPI의 `Depends(get_db)`를 통해 의존성 주입하면 편리
- **Session 팩토리**
	- 사용 코드
		- **`SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)`**
		- `db = SessionLocal()`
		- `db.close()` - 사용 후에는 직접 끊어줘야 함
	- `sessionmaker` 옵션
		- `autocommit`
			- 세션 작업 후 자동으로 커밋되도록 활성화
			- `False`로 두고 **명시적으로 커밋하는게 좋음**
		- `autoflush`
			- 트랜잭션 안에서 바로바로 데이터 반영 시킬지 여부
			- 예를 들어, DB에 100개의 데이터가 있는데 현재 트랜잭션 내에서 insert 쿼리 후 count 쿼리를 날리면, autoflush가 true일 때 101개 결과를 반환
			- **과거 방식이기도 하고, `False`가 바람직**
## 테이블 초기 생성
```python
Base.metadata.create_all(bind=engine)
```
## 조회 Syntax
- 모든 컬럼 조회
	- `db.query("TableObjectName")`
	- = `SELECT * FROM TableName`
	- e.g. `db.query(User)`
- 특정 컬럼 조회
	- `db.query("TableObjectName.columnname")`
	- = `SELECT columnname FROM TableName`
	- e.g. `db.query(User.email)`
- `WHERE`절
	- `filter`
		- e.g. `filter(User.nickname == 'veluga')`
	- `filter_by`
		- e.g. `filter_by(nickname="john")`
- `AND` & `OR`
	- AND
		- e.g. `filter("조건").filter("조건")`
		- e.g. `filter("조건", "조건")`
	- OR
		- `or_`을 임포트해 사용
		- `from sqlalchemy import or_`
		- e.g. `filter(or_(User.username == "veluga", User.id == 1))`
- 정렬
	- 오름차순 정렬
		- `order_by(User.id)`
	- 내림차순 정렬
		- `from sqlalchemy import desc`
		- `order_by(desc(User.id))`
- 조회 실행 (쿼리 실행)
	- 단건 조회
		- `first()`
			- 결과가 여러 개면 그 중 첫 번째 리턴
			- 없을 경우 `None` 반환
		- `one()`
			- 결과가 여러 개거나 없을 경우 에러
		- `scalar()`
			- 결과가 여러 개일 경우 에러
			- 없을 경우 `None` 반환
	- 복수 리스트 조회
		- `all()`
		- `scalars()`
- 조회 결과의 개수 반환
	- `count()`
- 그룹화 및 집계 함수 사용 패턴
	- `func`에서 원하는 집계함수 사용 (`count`, `sum`, `max`, `min`...)
	```python
	from sqlalchemy import func
	
	db.query(func.count(User.id).label('total')).group_by(User.id).all()
	```
## 삭제 Syntax
```python
db.delete("조회한 모델 객체")
db.commit()
```

***
## Reference
[2.0 style query 결과 가져오기 총 정리 (한 개 또는 여러 개)](https://velog.io/@ohdowon064/sqlalchemy-2.0-%EC%8A%A4%ED%83%80%EC%9D%BC-%EC%BF%BC%EB%A6%AC-%EA%B2%B0%EA%B3%BC%ED%95%9C-%EA%B0%9C-%EC%97%AC%EB%9F%AC-%EA%B0%9C-%EA%B0%80%EC%A0%B8%EC%98%A4%EA%B8%B0-%EC%B4%9D-%EC%A0%95%EB%A6%AC)
[SQLAlchemy 1.x 와 2.0의 Query 스타일 비교](https://daco2020.tistory.com/324)