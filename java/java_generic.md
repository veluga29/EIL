## 제네릭(Generic)
- **제네릭 타입**
	```java
	public class GenericBox<T> {
	
		private T value;
		
		public void set(T value) {
			this.value = value;
		}
		
		public T get() {
			return value;
		}
	}
	```
	- **타입 매개변수를 사용**하는 **클래스**나 **인터페이스**
	- 핵심: **타입 결정을 생성시점으로 미룸**
		- **타입 매개변수** 선언 e.g. `T`
		- 생성 시점에 **타입 인자** 전달 e.g. `Integer`, `String`...
		- **컴파일 과정**에서 타입 정보 반영
	- 유의점
		- 지정할 수 있는 타입은 **참조형만 가능** (기본형 X, **래퍼 클래스 O**)
		- 한번에 **여러 타입 매개변수** 선언 가능
			- `class Data<K, V> {}`
		- **Raw 타입은 사용하지 말아야 함**
			- 원시 타입(Raw Type): 제네릭 타입 생성시 타입 지정을 하지 않는 것
				- `GenericBox integerBox = new GenericBox();`
				- 내부에서 타입 매개변수에 **`Object`** 사용
			- 원시 타입은 **과거 코드와 하위 호환**을 위해 존재하므로 **사용해서는 안됨**
			- `Object` 타입을 사용해야 하는 경우, 타입 인자로 전달하자
				- `GenericBox<Object> integerBox = new GenericBox<>();`
	- 선언 방법
		- **`class GenericBox<T>`**
		- 클래스 명 오른쪽에 `<>`(다이아몬드)를 사용해 타입 매개변수(`T`) 선언
		- 클래스 내부에 `T` 타입이 필요한 곳에 `T` 적용
	- 생성 방법
		- `GenericBox<Integer> integerBox = new GenericBox<Integer>();`
			- 생성 시점에 원하는 **타입 인자** 지정
		- **`GenericBox<Integer> integerBox = new GenericBox<>();`**
			- 자바 컴파일러의 **타입 추론**을 사용해 생성 부분에 **타입 정보 생략**도 가능
- 제네릭 용어 정리
	- **제네릭 타입** (Generic Type)
		- **타입 매개변수를 사용**하는 **클래스**나 **인터페이스** (제네릭 클래스, 제네릭 인터페이스)
		- e.g. `GenericBox<T>`
	- **타입 매개변수** (Type Parameter)
		- 제네릭 타입이나 메서드에서 사용되는 **변수**
		- e.g. `T`, `E`, `K`, `V`
	- **타입 인자** (Type Argument)
		- 제네릭 타입을 사용할 때 **전달**되는 **실제 타입**
		- e.g. `Integer`, `String`...
- 타입 매개변수 **명명 관례**
	- 일반적으로 **용도에 맞는 단어의 첫글자**를 **대문자**로 사용
	- 종류
		- E - Element
		- K - Key
		- N - Number
		- T - Type
		- V - Value
		- S, U, V etc. - 2nd, 3rd, 4th types
- 제네릭의 필요성
	- 중복 제거
		- 중복 제거 시도 과정
			- 문제 상황: 데이터 보관 및 꺼내는 객체 만들기
				- **서로 다른 타입의 데이터**를 어떻게 다룰지 문제
			- 해결 1: 각 타입별 클래스 정의 (`IntegerBox`, `StringBox`)
				- **코드 재사용X**, 타입 안정성 O
					- `XxxBox` 클래스 수십개 만드는 것은 비효율적
			- 해결 2: 다형성을 활용한 하나의 클래스 정의 (`ObjectBox`)
				- 코드 재사용 O, **타입 안정성 X**
					- 반환 타입이 `Object`라 사용 시 위험한 **다운 캐스팅 필요**
					- 실수로 **잘못된 타입의 인수가 전달**될 수 있는 문제 (입력 제약이 느슨)
				- e.g. 
					- `integerBox.set("문자100");`
					- `Integer result = (Integer) integerBox.get();`
					- -> **`ClassCastException` 발생**
			- 해결 3: **제네릭 클래스** 사용하기
				- **코드 재사용 O**, **타입 안정성 X**

>**코드 재사용성 상승시키기**
>
>프로그래밍 세계에서 **무언가에 대한 결정**을 정의 시점이 아니라 **사용 시점으로 미루면 재사용성이 크게 상승**한다. 즉, **매개변수**를 활용해 **실행 시점에 인자를 전달**하면 재사용이 크게 상승한다. (e.g. 제네릭, 메서드...)