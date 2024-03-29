## FastAPI와 비동기
- FastAPI는 **비동기 처리**에 최적화 (비동기 코드를 사용한다면 성능 이점)
- 비동기 작업
	- CPU와 RAM 간의 작업이 1이라고 한다면, CPU에서 IO 작업은 1000, 10000이 걸린다고 생각할 수 있음
	- IO 작업시 쉬고 있는 CPU가 다른 작업을 할 수 있다면, 성능이 대폭 상승
- 비동기 처리를 사용하면 **단일 스레드에서 여러 작업을 효율적으로 수행 가능**
	- 데이터베이스 쿼리나 HTTP 요청 같이 **I/O bound 작업의 성능을 크게 향상**시킴
- **이점을 볼 수 있는 상황**
	- **비동기 작업은 프로그램에서 지원해주어야 사용 가능**
	- 내부적 관점
		- **프레임워크 내부적인 처리**에서 IO 작업들을 비동기로 처리할 것이므로 성능 향상
		- IO 작업은 따로 없더라도 **비동기 코드를 작성했다면 내부적으로도 성능 이점**을 얻을 수 있음
	- 프로그램적 관점
		- CPU 작업만하는 코드로 이루어진 프로그램은 성능상 크게 이점을 보기 어려움
		- **작성한 코드가 IO 작업을 필요로 한다면 성능 향상**
			- 조건
				- **코드를 비동기적으로 짜야 함**(`async`, `await`)
				- 사용하는 **IO 라이브러리 자체가 비동기를 지원해야 함**
				- **프레임워크가 비동기를 지원**해야 함 (**FastAPI**)
	- 웹서비스 시나리오
		- 쿼리가 3초 걸리는 API 경로로 10개 요청이 동시에 온다면, 동기적 처리 상황 시 첫 사람은 3초가 걸리지만, 10번째 사람은 30초 걸림
		- 비동기적으로 처리할 시, 뒷사람들의 대기시간도 기존 최대 30초보다 훨씬 줄어들 것
## async/await
- `async def`
	- **비동기 함수를 정의**
	- Future 객체 반환
- `await`
	- 비동기 함수 내에서 사용
	- 특정 비동기 연산이 완료될 때까지 함수의 실행을 **일시적으로 중단하고 해당 연산의 완료를 기다림**
- 이벤트 루프
	- 프로그램 진입점에서 실행
	- `asyncio.run(main())` 같이 사용해 주어진 코루틴을 실행하고 완료될 때까지 이벤트 루프를 유지
	- 어떤 이벤트를 등록해두고 **특정 이벤트가 발생했는지 여부를 지속적으로 체크** (내부 반복문)
	- **실행되던 비동기 함수가 종료되면서 이벤트를 발생**시키고, 그 후 **await 뒷 부분의 코드가 이어서 실행됨**
- `asyncio.gather`
	- **여러 코루틴을 동시에 실행**
	- 모든 코루틴이 완료될 때까지 기다린 후, 코루틴 결과를 포함하는 리스트 반환