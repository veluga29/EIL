## DB 접근 기술 의존 관계 Flow
- JDBC 활용 기술 (SQLMapper, ORM)
	- -> **DataSource** 인터페이스
		- -> **Connection Pool의 DataSource 구현체** 사용
			- 초기화 과정: -> DriverManager -> JDBC `Connection` 인터페이스 (DB Driver)
		- -> **`DriverManagerDataSource`** DataSource 구현체 사용
			- 매 연결 시: -> DriverManager -> JDBC `Connection` 인터페이스 (DB Driver)
			- 항상 새 커넥션 생성
## 데이터베이스 변경 문제
- 일반적인 애플리케이션 서버와 DB 사용법
	- 커넥션 연결 (TCP/IP)
	- SQL 전달 (with 커넥션)
	- 결과 응답
- 문제는 **데이터베이스마다 사용법이 모두 다름** (커넥션 연결, SQL 전달, 결과 응답 방법)
- 데이터베이스 변경시 **애플리케이션의 DB 사용코드도 함께 변경**해야 하고, **개발자의 학습량**이 늘어남
## JDBC 표준 인터페이스(Java Database Connectivity)
![jdbc interface](../images/jdbc_interface.png)
- 자바에서 **여러 데이터베이스에 편리하게 접속**할 수 있도록 도와주는 3가지 **표준 인터페이스**
	- **`java.sql.Connection`** (커넥션 연결)
	- **`java.sql.Statement`** (SQL을 담은 내용)
	- **`java.sql.ResultSet`** (결과 응답)
- **JDBC 드라이버**
	- 각각의 DB 벤더들이 JDBC 인터페이스를 자신의 DB에 맞도록 **구현한 라이브러리**
	- 예시: MySQL JDBC 드라이버,  Oracle JDBC 드라이버 etc...
- 장점
	- 애플리케이션 로직이 **JDBC 표준 인터페이스에만 의존**하므로, 
	  DB 변경시 **애플리케이션 코드를 그대로 유지**하고 **JDBC 구현 라이브러리만 변경 가능**
	- **개발자는 JDBC 표준 인터페이스 사용법만 학습**하면, 모든 DB 연결 가능
- 한계
	- DB마다 SQL 역시 사용법의 차이가 있어, DB 변경 시 **여전히 SQL은 그에 맞도록 변경**해야 함
	- 다만, JPA를 사용하면 이 역시도 많은 부분 해결됨
## JDBC 활용 기술
- JDBC(1997)는 오래된 기술이고 사용 방법도 복잡
- 직접 사용하기보다는 이를 **편리하게 사용할 수 있는 다른 기술들을 활용** (내부에서 JDBC 사용)
- **SQL Mapper**
	![sql mapper](../images/sql_mapper.png)
	- 장점
		- SQL 응답 결과를 **객체로 편리하게 변환**
		- **JDBC 반복 코드를 제거**
		- 낮은 러닝커브 (SQL만 작성할 줄 알면 금방 배워 사용 가능)
	- 단점
		- 개발자가 직접 SQL 작성
	- Spring JdbcTemplate, MyBatis
- **ORM**
	![orm jpa jdbc](../images/orm_jpa_jdbc.png)
	- 장점
		- SQL 직접 작성하지 않아 **개발 생산성** 크게 상승 (**SQL을 동적으로 생성 및 실행**)
		- 데이터베이스마다 **다른 SQL을 사용하는 문제를 중간에서 해결**
	- 단점
		- 러닝커브가 높음
	- JPA (하이버네이트, 이클립스 링크...)
## JDBC DriverManager
![jdbc driver manager flow](../images/jdbc_driver_manager_flow.png)
- **`DriverManager`** (JDBC가 제공)
	- **라이브러리에 등록된 DB 드라이버들을 관리**
	- **커넥션 획득** 기능 제공 (JDBC 표준 인터페이스 **`Connection`**)
- **커넥션 획득 과정**
	- `Connection connection = DriverManager.getConnection(URL, USERNAME, PASSWORD);`
	- `DriverManager`는 라이브러리에 등록된 DB 드라이버 목록을 **자동으로 인식**
	- 드라이버들에게 순서대로 다음 정보를 넘겨서 **커넥션 획득 가능 여부 확인**
		- 접속에 필요한 정보: URL, 이름, 비밀번호...
		- 커넥션 획득 가능한 드라이버는 바로 **실제 DB에 연결해 커넥션 구현체 반환** 
		  (URL 등을 통해 판단)
		- 커넥션 획득 불가능한 드라이버는 다음 드라이버에게 순서를 넘김
- 커넥션 사용
	- 쿼리 준비
		- **`PreparedStatement`**(`pstmt`) 주로 사용 (**`Statement`의 자식 인터페이스**)
			- 전달할 SQL과 파라미터로 전달할 데이터를 바인딩
			- 파라미터 바인딩 방식은 SQL Injection 예방
		- 코드
			- `pstmt = con.prepareStatement(sql)`
			- `pstmt.setString(1, member.getMemberId)`
			- `pstmt.setInt(2, member.getMoney())`
	- 쿼리 실행
		- 조회
			- **`executeQuery()`**
				- `SELECT` 쿼리 조회 후 `ResultSet` 반환
				- `rs = pstmt.executeQuery()`
			- **`ResultSet`** (JDBC 표준 인터페이스)
				- `executeQuery()`의 반환 타입
				- `select` 쿼리 결과가 순서대로 들어가 있음
				- 내부의**커서**(Cursor)를 이동해 다음 데이터 조회 (`rs.next()`)
					- **최초 커서는 데이터를 가리키고 있지 않아서**, 한 번 `rs.next()` 호출해야 조회 가능
					- `rs.next()` 결과가 true면 데이터 있음, false면 데이터 없음
					- 원하는 커서 위치에서 키 값으로 데이터 획득 (`rs.getString`, `rs.getInt`...)
		- 갱신
			- **`executeUpdate()`**
				- 갱신 쿼리 실행 후 영향 받은 Row 수 반환
				- `pstmt.executeUpdate()`
## 커넥션 풀 (Connection Pool)
- 애플리케이션 시작 시점에 필요한 만큼 **커넥션을 미리 생성**해 풀에 **보관**
	- `DriverManager` 사용
	- 애플리케이션 실행 속도에 영향을 주지 않기 위해 **별도의 쓰레드로 커넥션 생성**
	  ("poolName" connection adder)
- 풀 내에 커넥션은 모두 TCP/IP로 **DB와 연결**되어 즉시 SQL 전달 가능
- **애플리케이션 로직**은 커넥션 풀을 통해 **이미 생성된 커넥션 획득 및 반환**
	- 애플리케이션 로직은 **DB 드라이버를 통하지 않음**
	- 커넥션을 단순히 **객체 참조**로 가져다 쓰면 됨
	- 다 사용한 **커넥션은 종료하지 않고** 그대로 커넥션 풀에 반환
- **적절한 커넥션 풀 숫자**
	- 스펙에 따라 다르기 떄문에 **성능 테스트**를 통해 정해야 함 (기본값은 보통 10개)
	- 서버 당 최대 커넥션 수를 제한 가능 (무한정 커넥션 생성을 막아 DB 보호)
- **hikariCP** 주로 사용

>커넥션 풀 없는 DB 커넥션 획득 과정
>
>1. 애플리케이션 로직이 DB 드라이버 통해 커넥션 조회 (TCP/IP 연결, 3 way handshake)
>2. 연결 후 드라이버는 ID, PW 및 기타 부가정보를 DB에 전달
>3. DB는 ID, PW로 내부 인증을 하고, 내부에 DB 세션을 생성 후 커넥션 생성 완료 응답
>4. DB 드라이버는 커넥션 객체를 생성해서 클라이언트에 반환
>
>-> 통신에 **리소스가 많이 들고**, 커넥션 생성 시간이 매번 추가되어 **사용자 경험이 안좋아짐**
>
>그래서 **커넥션 풀은 이 과정을 애플리케이션 시작 시점에 미리 진행**

>커넥션 생성 시간
>
>커넥션 생성시간은 MySQL 계열 DB에서는 수 ms 정도로 매우 빨리 커넥션을 확보한다. 
>반면에 수십 ms 이상 걸리는 DB들도 있다.

## DataSource 인터페이스
![datasource interface](../images/datasource_interface.png)
- 문제
	- 커넥션을 획득하는 다양한 방법 존재
		- JDBC DriverManager 직접 사용 (신규 커넥션 생성)
		- DBCP2 커넥션 풀
		- HikariCP 커넥션 풀
	- 커넥션 획득방법을 변경하면 **애플리케이션 코드도 함께 변경해야 함**
- **`DataSource`** (해결책)
	- **커넥션을 획득하는 방법을 추상화**한 인터페이스
	- 핵심 기능은 **커넥션 조회** (`getConnection` -> `Connection`)
	- 애플리케이션 코드는 **`DataSource`에 의존**
		- 각각의 **커넥션풀의 `DataSource` 구현체**를 갈아끼우기 (`HikariDataSource`)
		- `DriverManager`의 경우 `DataSource` 구현체로 **`DriverManagerDataSource`** 사용
	- 인터페이스에 의존하므로, 커넥션 획득 방법 변경해도 **애플리케이션 코드 변경 X** (**DI + OCP**)
	- `JdbcUtils`를 사용하면 커넥션도 편리하게 닫을 수 있음

>DataSource와 DriverManager의 차이
>
>`DriverManager`는 커넥션 획득마다 매 번 설정정보(URL, USERNAME, PASSWORD...)를 넘겨줘야 한다.
>반면에, `DataSource`는 객체 생성시 **한 번만 설정정보를 전달**하고 이후 커넥션 획득은 `dataSource.getConnection`만 호출한다.
>이렇게 **설정과 사용을 분리**하면 설정 정보를 한 곳에 모아두고 이에 대한 의존성을 없앨 수 있다. (예를 들어, `Repository`는 `DataSource`만 의존하고 설정정보를 몰라도 된다.)

## DB 연결구조와 DB 세션
![connection session flow](../images/connection_session_flow.png)
- 사용자는 WAS, DB 접근 툴 같은 클라이언트를 사용해 DB 서버에 접근
- DB 서버에 연결을 요청하고 **커넥션**을 맺음
- DB 서버는 내부에 커넥션에 대응하는 **세션**을 생성
	- 해당 커넥션을 통한 **모든 요청은 세션이 실행** (SQL 실행 및 트랜잭션 제어)
	- 커넥션 풀이 10개의 커넥션을 생성하면 세션도 10개 생성
- **사용자가 커넥션을 닫거나 DBA가 세션을 강제 종료**하면 **세션 종료**
## 애플리케이션 구조와 트랜잭션
![](../images/application_3_layer_architecture.png)
- 3계층 아키텍처 (가장 단순하면서 많이 사용)
	- 프레젠테이션 계층
		- UI 관련 처리
		- 웹 요청과 응답, 사용자 요청 Validation
		- 사용 기술: 서블릿, 스프링 MVC
	- **서비스 계층**
		- **비즈니스 로직**
		- 사용 기술: 가급적 특정 기술 의존 없이 **순수 자바 코드**
			- 시간이 흘러서 UI(웹), 데이터 저장 기술을 다른 기술로 변경해도 
			  **비즈니스 로직은 최대한 변경 없이 유지해야 함**
			- 덕분에 비즈니스 로직의 **유지보수와 테스트가 쉬움**
	- 데이터 접근 계층
		- 실제 데이터베이스에 접근하는 코드
		- 사용 기술: JDBC, JPA, File, Redis, Mongo...
- 트랜잭션은 **비즈니스 로직이 있는 서비스 계층에서 시작**해야 함 
	- **서비스 계층에서 커넥션 생성 및 종료**
	- 비즈니스 로직이 잘못되면 다같이 롤백
- 이를 위해, **트랜잭션 추상화**가 필요
	- 추상화 없는 경우, 같은 커넥션을 유지하기 위해 **커넥션을 파라미터로 전달**하는 단순한 방법 사용
	- 그 결과 DB 접근 기술인 트랜잭션으로 인해 **순수한 서비스 계층에 의존성 발생**
## 트랜잭션 추상화
```java
public interface TxManager {
	begin();
	commit();
	rollback();
}
```
- **트랜잭션 추상화 인터페이스를 의존**하도록 하면, **순수한 서비스 계층**을 만들 수 있음
- DB 접근 기술을 변경할 때, 해당 기술에 맞는 구현체를 만들면 됨 (DI + OCP)
	- `JdbcTxManager`, `JpaTxManager`
## 스프링 트랜잭션 추상화
- **`PlatformTransactionManager`** 인터페이스 (=**트랜잭션 매니저**)
	![PlatformTransactionManager](../images/platform_transaction_manager.png)
	- **스프링**이 제공하는 트랜잭션 추상화
	- **트랜잭션 추상화** 역할
		- 데이터 접근 기술에 따른 **트랜잭션 구현체**도 **스프링**이 제공
			- 스프링 5.3부터 JDBC 트랜잭션 관리 시, **`JdbcTransactionManager`** 제공
			- `DataSourceTransactionManager`를 상속 받아 약간의 기능 확장이 있지만, 같은 것으로 보기
		- 메소드
			- **`getTransaction()`**
				- 트랜잭션을 시작
				- 기존에 **이미 진행 중인 트랜잭션**이 있는 경우, **해당 트랜잭션에 참여**
			- `commit()`
			- `rollback()`
	- **리소스 동기화** 역할
		![TransactionSynchronizationManager](../images/transaction_synchronization_manager.png)
		- **스프링**은 **트랜잭션 동기화 매니저**를 제공
			- 트랜잭션 유지를 위해, 트랜잭션의 시작부터 끝까지 **같은 커넥션을 동기화**(유지)하도록 도움
			- **트랜잭션 매니저 내부**에서 트랜잭션 동기화 매니저를 **사용**
		- **쓰레드 로컬** (**`ThreadLocal`**)을 사용해 커넥션 동기화
			- **멀티스레드 상황**에도 **커넥션**을 문제 없이 **안전하게 보관**해주는 것
			- **쓰레드마다 별도의 저장소**가 부여되어, 해당 쓰레드만 해당 데이터에 접근 가능
		- 코드가 지저분해지는 단순한 커넥션 **파라미터 전달 방법을 피할 수 있음**
	- 커넥션 획득 및 종료 과정
		- 트랜잭션 매니저는 데이터 소스를 통해 커넥션을 생성하고 트랜잭션 시작
		- 트랜잭션 매니저는 해당 **커넥션**을 **트랜잭션 동기화 매니저에 보관**
		- 리포지토리는 **트랜잭션 동기화 매니저에 보관된 커넥션**을 꺼내서 사용
		- 트랜잭션 매니저는 트랜잭션 동기화 매니저에 보관된 커넥션으로 트랜잭션을 종료
		- 이어서 커넥션을 닫음
