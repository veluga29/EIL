## 제네릭 타입(Generic)
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
		- **`static` 메서드**에 **타입 매개변수**를 **사용 불가**
			```java
			class Box<T> {
				T instanceMethod(T t) {} //가능
				static T staticMethod1(T t) {} //제네릭 타입의 T 사용 불가능
			}
			```
			- 제네릭 타입은 **객체 생성시점에 타입이 정해짐**
			- `static` 메서드는 **클래스 단위**로 작동하므로 인스턴스 생성과 무관
			- **`static` 메서드에 제네릭을 도입**하려면 **제네릭 메서드**를 사용해야 함
	- 선언 방법
		- **`class GenericBox<T>`**
		- 클래스 명 오른쪽에 `<>`(다이아몬드)를 사용해 타입 매개변수(`T`) 선언
		- 클래스 내부에 `T` 타입이 필요한 곳에 `T` 적용
	- 생성 방법
		- `GenericBox<Integer> integerBox = new GenericBox<Integer>();`
			- 생성 시점에 원하는 **타입 인자** 지정
		- **`GenericBox<Integer> integerBox = new GenericBox<>();`**
			- 자바 컴파일러의 **타입 추론**을 사용해 생성 부분에 **타입 정보 생략**도 가능
			- **컴파일러가 대신 타입 인자 전달**
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

>**코드 재사용성 상승시키기**
>
>프로그래밍 세계에서 **무언가에 대한 결정**을 정의 시점이 아니라 **사용 시점으로 미루면 재사용성이 크게 상승**한다. 즉, **매개변수**를 활용해 **실행 시점에 인자를 전달**하면 재사용이 크게 상승한다. (e.g. 제네릭, 메서드...)

## 제네릭의 필요성
- 제네릭은 **코드 재사용성**과 **타입 안정성**을 **동시에** 잡으면서 중복 제거 가능
- 중복 제거 시도 과정
	- 문제 상황: 데이터 보관 및 꺼내는 객체 만들기
		- **서로 다른 타입의 데이터**를 어떻게 다룰지 문제
	- 해결 1: 각 타입 별 클래스 정의 (`IntegerBox`, `StringBox`)
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
		- **코드 재사용 O**, **타입 안정성 O**
## 타입 매개변수 상한
- **타입 매개변수 상한**은 제네릭의 **타입 안정성**을 더욱 견고히 지킴
	- e.g. `<T extends Animal>`
		- 타입 매개변수를 `Animal`과 그 자식만 받을 수 있도록 제한
	- 덕분에 **자바 컴파일러**가 타입 매개변수에 입력될 수 있는 **값 범위를 예측** 가능
- 제네릭의 타입 안정성 개선 과정
	- 문제 상황: **개 병원은 개만** 받을 수 있고, **고양이 병원은 고양이만** 받을 수 있어야 함
	- 해결 1: 각 타입 별 클래스 정의 (`DogHospital`, `CatHospital`)
		- **코드 재사용X**, 타입 안정성 O
	- 해결 2: 다형성을 활용한 하나의 클래스 정의 (`AnimalHospital`)
		- 코드 재사용 O, **타입 안정성 X**
			- 반환 타입이 `Animal`라 사용 시 위험한 **다운 캐스팅 필요**
			- 실수로 **잘못된 타입의 인수가 전달**될 수 있는 문제 (입력 제약이 느슨)
	- 해결 3: 제네릭 도입
		- 여전히 **타입 안정성이 불안**
			- **원치 않는 타입의 인수 전달**되는 문제
			- 타입 매개변수(`T`)가 `Object` 타입으로 취급되어 `Animal`의 기능 사용 불가
	- 해결 4: **타입 매개변수 상한**
		- 코드 재사용 O, **타입 안정성 O**
			- **올바른 타입의 인수 전달**이 가능해져 타입 안정성 향상
			- 상위 타입의 **원하는 기능 사용 가능**
## 제네릭 메서드
```java
public class GenericMethod {

	public static Object objMethod(Object obj) {
        System.out.println("object print: " + obj);
        return obj;
	}
    
    public static <T> T genericMethod(T t) {
        System.out.println("generic print: " + t);
        return t;
	}
    
    public static <T extends Number> T numberMethod(T t) {
        System.out.println("bound print: " + t);
        return t;
	}

}
```
- **타입 매개변수를 사용**하는 메서드
	- 클래스 전체가 아니라 **특정 메서드 단위로 제네릭을 도입할 때 사용**
- 핵심: 타입 결정을 **메서드 호출 시점으로 미룸**
- 선언 방법
	- **`public static <T> T genericMethod(T t) {...}`**
		- 반환타입 왼쪽에 타입 매개변수 선언
- 호출 방법
	- `GenericMethod.<Integer>genericMethod(10)`
		- 호출 시점에 원하는 **타입 인자** 지정
	- **`Integer integerValue = GenericMethod.numberMethod(10);`**
		- 보통 타입 추론을 통해 **생략해 사용**
		- 자바 컴파일러는 전달되는 **인자 타입**과 **반환 타입**을 보고 **타입 추론**
		- **컴파일러가 대신 타입 인자 전달**
- **타입 매개변수 상한** 가능
	- `public static <T extends Number> T numberMethod(T t) {}`
- 유의점
	- **인스턴스 메서드**, **`static` 메서드** 모두 적용 가능 (제네릭 타입은 `static` 메서드에 사용 불가)
		```java
		class Box<T> { //제네릭 타입
			static <V> V staticMethod(V t) {} //static 메서드에 제네릭 메서드 도입 
			<Z> Z instanceMethod(Z z) {} //인스턴스 메서드에 제네릭 메서드 도입 가능
		}
		```
	- **제네릭 타입**과 **제네릭 메서드**의 **타입 매개변수 이름은 다르게** 하자! (모호함 X)
		- 인스턴스 메서드 동시 적용에서 제네릭 메서드가 우선순위 가지지만 **모호한 것은 좋지 않다!**
		```java
		public class ComplexBox<T extends Animal> {
		     
		    private T animal;
		
			public void set(T animal) {
		        this.animal = animal;
			}
			
			public <T> T printAndReturn(T t) {
				System.out.println("animal.className: " + animal.getClass().getName());
				System.out.println("t.className: " + t.getClass().getName());
				
				// 호출 불가! 메서드는 <T> 타입이다. <T extends Animal> 타입이 아니다.
				// t.getName(); 
				return t;
			}
		
		}
		```
## 와일드 카드 (Wild Card)
```java
public class WildcardEx {

	//이것은 제네릭 메서드이다.
	//Box<Dog> dogBox를 전달한다. 타입 추론에 의해 타입 T가 Dog가 된다.
    static <T> void printGenericV1(Box<T> box) {
        System.out.println("T = " + box.get());
	}

	//이것은 제네릭 메서드가 아니다. 일반적인 메서드이다.
	//Box<Dog> dogBox를 전달한다. 와일드카드 ?는 모든 타입을 받을 수 있다.
    static void printWildcardV1(Box<?> box) {
        System.out.println("? = " + box.get());
	}
    
    static <T extends Animal> void printGenericV2(Box<T> box) {
        T t = box.get();
		System.out.println("이름 = " + t.getName()); 
	}
	
	static void printWildcardV2(Box<? extends Animal> box) { 
		Animal animal = box.get();
		System.out.println("이름 = " + animal.getName());
	}
    
    static <T extends Animal> T printAndReturnGeneric(Box<T> box) {
        T t = box.get();
		System.out.println("이름 = " + t.getName());
		return t;
	}
	
    static Animal printAndReturnWildcard(Box<? extends Animal> box) {
        Animal animal = box.get();
        System.out.println("이름 = " + animal.getName());
        return animal;
    }
    
}
```
- **이미 타입이 정해진 제네릭 타입**을 **편리하게 전달** 받을 수 있도록 하는 키워드 (`*`, `?`)
	- **와일드 카드**는 제네릭 타입, 제네릭 메서드를 **선언하는 것이 아님**
- 단순히 **일반 메서드**에 **제네릭 타입**을 받을 수 있는 **매개변수**가 하나 있는 것
- 따라서, **일반적인 메서드**에 사용 가능
	- e.g. `Box<Dog>`, `Box<Cat>` 등의 제네릭 타입을 전달 받음
- 유의점
	- 제네릭 타입, 제네릭 메서드와 달리, 호출 시에 **타입을 지정할 필요가 없음**
	- **비제한** 와일드카드 (`?`)
		- 모든 타입을 다 받을 수 있음
	- **상한 와일드 카드** & **하한 와일드카드** 사용 가능
		- **상한 와일드 카드**
			- `static void printWildcardV2(Box<? extends Animal> box) {...}`
			- `Animal` 타입 + `Animal` 타입의 **하위 타입**만 입력 가능
		- **하한 와일드 카드** (제네릭 타입, 제네릭 메서드에는 없음)
			- `static void writeBox(Box<? super Animal> box) {...}`
			- `Animal` 타입 + `Animal` 타입의 **상위 타입**만 입력 가능 (e.g. `Box<Dog>` 전달 불가)
- **사용 전략**
	- 보통의 경우 **더 단순한 와일드 카드 사용 권장**
		- 제네릭 메서드처럼 **타입을 전달해 결정하는 것**은 내부 과정이 **복잡**
	- **꼭 필요한 상황**에만 제네릭 타입/제네릭 메서드로 정의
		- 전달할 타입을 **명확하게 반환**해야 하는 경우 제네릭 타입, 제네릭 메서드 사용
			- `Dog dog = WildcardEx.printAndReturnGeneric(dogBox)`
		- 전달할 타입을 **명확하게 반환하지 않아도 되는 경우** 와일드 카드 사용
			- `Animal animal = WildcardEx.printAndReturnWildcard(dogBox)`
