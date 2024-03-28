## 비동기 SQLAlchemy
- SQLAlchmey 1.4 이상부터 비동기 문법 지원 시작
- 비교적 최근에 나와 문법이 불안정한 느낌이지만, **DB 비동기 처리는 FastAPI의 성능을 크게 향상 시킬 지점**
- 주요 비동기 지원 모듈: **`sqlalchemy.ext.asyncio`**
	- `create_async_engine` (비동기 데이터베이스 엔진)
	- `AsyncSession` (비동기 세션)
	- `sessionmaker(class_=AsyncSession)` (비동기 세션 팩토리)
		- 기존 `sessionmaker`에 `class_`만 추가
## 비동기 Session 사용법
```python
AsyncSessionLocal =  sessionmaker(autocommit=False, autoflush=False, bind=engine, class_=AsyncSession)

async def get_db():
    async with AsyncSessionLocal() as session:
        yield session
```
## 테이블 초기 생성
```python
async with engine.begin() as conn:
    await conn.run_sync(Base.metadata.create_all)
```

## 조회 Syntax (2.0 스타일 코드)
- 조회 실행
	- `await db.execute(...)`
	- `select` 객체로 SQL 쿼리를 조합해 `execute` 에 넣어 실행
- 모든 컬럼 조회
	- `sqlalchemy.future` 패키지에서 `select`를 import
	- e.g. `select(User)`
- 특정 컬럼 조회
	- e.g. `select(User.email)`
- `WHERE`절
	- `filter`
		- e.g. `filter(User.nickname == 'veluga')`
- 정렬
	- 오름차순 정렬
		- `order_by(User.id)`
- 조회 방법
	- 단건 조회
		- `scalar()`
	- 복수 리스트 조회
		- `scalars().all()`
- 조회 결과의 개수 반환
	```python
	from sqlalchemy import func
	
	result = await db.execute(select(func.count()).select_from(select(User)))
	count = result.scalars().one()
	```
- 그룹화 및 집계 함수 사용 패턴
	- `func`에서 원하는 집계함수 사용 (`count`, `sum`, `max`, `min`...)
	```python
	from sqlalchemy import func
	
	result = await db.execute(select(func.count(User.id)).group_by(User.id))
	result.scalars().all()
	```
## 삭제 Syntax
```python
await db.delete("조회한 모델 객체")`
await db.commit()
```