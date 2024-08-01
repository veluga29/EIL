## java.lang 패키지
- 자바 언어를 이루는 **가장 기본이 되는 클래스들을 보관**하는 **패키지**
- 모든 자바 애플리케이션에서 자동으로 import됨 (**import 생략 가능**)
- 대표 클래스
	- `Object`: 모든 자바 객체의 부모 클래스
	- `String`: 문자열
	- `Integer`, `Long`, `Double`: 래퍼타입, 기본형 데이터 타입을 객체로 만든 것
	- `Class`: 클래스 메타 정보
	- `System`: 시스템과 관련된 기본 기능들 제공
## Object 클래스
- 자바에서 모든 클래스의 **최상위 부모 클래스**
- 클래스에 상속 받을 부모 클래스가 없으면 **묵시적으로 `Object` 클래스를 상속 받음**
	- `public class Parent {...}` == `public class Parent extends Object {...}`
- 묵시적 상속으로 인해 `Object`는 **메모리에도 함께 생성**됨
	![java object memory assignment](../images/java_object_memory_assignment.png)
- `Object` 클래스가 최상위 부모 클래스인 이유
	- **공통 기능 제공**
		- 모든 객체에 필요한 기본 기능을 구현
		- 모든 개발자들이 직접 만들 필요 없이 **일관성** 있게 사용 & 프로그래밍이 **단순화**
			- Object가 없다면 수많은 개발자들이 유사한 공통 부모 클래스를 구현해 일관성 X...
	- **다형성의 기본 구현** (한계 존재)
		- 다형성의 올바른 활용 = 다형적 참조 + 메서드 오버라이딩
			- 장점: 다형적 참조 가능
				- **모든 객체를 다 담을 수 있으므로**, 다양한 타입의 객체를 **통합적으로 처리** 가능
			- 한계: 메서드 오버라이딩 불가
				- 자식 클래스의 기능을 사용하려면 다운캐스팅을 해야만 함
				- `toString` 같은 **`Object`가 보유한 메서드는 당연히 오버라이딩 가능**
- 제공 메서드
	- `toString()`: 객체의 정보를 문자열 형태로 제공
		- 기본 로직: 패키지 포함 객체의 이름 + 16진수화된 객체의 참조값 (해시 코드)
		- **IDE를 통해 재정의하면 편리**
		- 참고) `System.out.println()` 메서드 내부에서 호출됨
			- `public void println(Object x) {...}`
			- -> `String.valueOf(x)` -> `(obj == null) ? "null" : obj.toString()`
			- **다형성을 활용한 OCP의 좋은 예**
				- `Object`를 인자로 받아 다형적 참조
				- Open: toString을 오버라이딩해 **기능 확장**
				- Closed: 클라이언트 코드인 `println()`은 **변경 X**
				- -> 덕분에 세상 모든 객체의 정보를 편리하게 출력 가능
	- `equals()`: 객체의 같음을 비교 (**동등성 비교**)
		- "두 객체가 같다"는 2가지 의미
			- **동일성**(Identity)
				- 두 객체가 **참조값이 같은 동일한 객체**인지 확인 (`==`)
				- **자바 머신** 기준, 메모리 참조, **물리적**
			- **동등성**(Equality)
				- 두 객체가 **논리적으로 동등**한지 확인 (`equals()`)
				- **사람** 기준, **논리적**
		- 기본 로직: `==` 동일성 비교 제공
		- **동등성 개념은 각각의 클래스마다 다르기 때문에**, 동등성이 필요한 경우 **재정의** (**IDE 활용**)
	- `getClass()`: 객체의 클래스 정보를 제공
	- `hashCode()`
	- `clone()`: 객체 복사 (잘 사용 X)
	- `notify()`, `notifyAll()`, `wait()`: 멀티 쓰레드용 메서드
	- ...

>객체의 참조값 출력
>
>`toString()`의 기본 사용 이외에 다음 코드를 사용하면 **객체의 참조값을 직접 출력**할 수 있다.
>
>**`String refValue = Integer.toHexString(System.identityHashCode(dog1));`**
>`System.out.println("refValue = " + refValue);`
> 
> 출력값: refValue = 72ea2f77

>`equals()` 메서드 구현 시 지켜야할 규칙 (중요 X)
>
>**반사성(Reflexivity)**: 객체는 자기 자신과 동등해야 한다. ( `x.equals(x)` 는 항상 `true` ).
>**대칭성(Symmetry)**: 두 객체가 서로에 대해 동일하다고 판단하면, 이는 양방향으로 동일해야 한다. (`x.equals(y)` 가 `true` 이면 `y.equals(x)` 도 `true` ).
>**추이성(Transitivity)**: 만약 한 객체가 두 번째 객체와 동일하고, 두 번째 객체가 세 번째 객체와 동일하다면, 첫 번째 객체는 세 번째 객체와도 동일해야 한다.
>**일관성(Consistency)**: 두 객체의 상태가 변경되지 않는 한, `equals()` 메소드는 항상 동일한 값을 반환해야 한다.
>**null에 대한 비교**: 모든 객체는 `null` 과 비교했을 때 `false` 를 반환해야 한다.