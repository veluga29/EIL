# 자바 객체 지향 설계
## 클래스가 필요한 이유
```java
String[] studentNames = {"학생1", "학생3", "학생4", "학생5"}; 
int[] studentAges = {15, 17, 10, 16};
int[] studentGrades = {90, 100, 80, 50};
```

학생이라는 개념을 다룰 때, 배열과 반복문으로 데이터를 처리해야 하므로 데이터 변경 시 실수할 가능성이 높다. 따라서 사람이 관리하기 좋은 코드를 만들기 위해 학생이라는 개념을 하나의 클래스로 묶어야 한다.

## 클래스 특징
- 클래스를 통해 마음껏 사용자 정의 타입을 만들 수 있다. (설계도)
- 클래스에 정의한 변수들 = 멤버 변수(Member variable) = 필드(Field)
- 실제 메모리에 만들어진 실체를 객체 혹은 인스턴스라 한다.
- 클래스 타입 변수는 객체를 생성하면 해당 객체의 **참조값**을 담는다.
	```java
		System.out.println(student);
		
		// 출력값
		// (패키지 + 클래스 정보 @ 16진수 참조값)
		class1.Student@7a81197d
	```

## 클래스 & 인스턴스 & 객체 
- 클래스
	- 객체 생성을 위한 '틀' 또는 '설계도'
	- 객체가 가져야 할 속성(변수)과 기능(메서드)를 정의한다.
- 인스턴스
	- 클래스로부터 생성된 객체
	- 인스턴스 = 객체
	- 어떤 클래스에 속해 있는지 강조 (관계에 초점)
- 객체
	- 클래스의 속성과 기능을 가진 실체
	- 세상 모든 사물을 단순하게 추상화해보면 속성과 기능 2가지만 남는다.


## 변수의 값 초기화
- 멤버 변수: 자동 초기화
	- 인스턴스 생성시 자동 초기화 (new로 만드는 객체들의 멤버 변수들은 모두 자동 초기화된다.)
	- `int` = `0`, `boolean` = `fasle`, `참조형` = `null`
	- 직접 초기화 지정 가능
- 지역 변수: 수동 초기화

## null
참조형 변수에서 아직 가리키는 대상이 없다면 null을 넣어둘 수 있다.
```java
Data data = null;
```

>아무도 참조하지 않는 인스턴스 (feat. GC)
>
>참조형 변수에 null을 할당하면 해당 참조 데이터가 메모리에 남아 있다가 GC(Garbage Collector)에 의해 제거된다.
>메소드가 종료되어 지역변수가 사라질 때, 지역변수가 참조하고 있던 인스턴스 역시 메모리에 남아 있다가 GC에 의해 제거된다.

## NullPointerException
`null`에 `.`을 찍을 때 발생하는 에러이므로 디버깅시 유의하자.

## `this`
인스턴스 자신의 참조값을 가리킨다. 생성자에서 지역변수 이름이 겹친다면 this를 통해 멤버변수에 접근할 수 있다.
`this`는 생략이 가능하다.
과거에는 명시적으로 보이지 않아 멤버 변수 접근시 항상 `this`를 사용하는 코딩 스타일이 존재했다. 
그러나 최근엔 IDE의 발달 덕분에 멤버변수와 지역변수 구분이 잘되기 때문에, 꼭 필요한 경우에만 사용하고 생략하는게 권장된다.

> 변수 탐색
> 변수를 찾을 때 가까운 지역변수(매개변수 포함)를 먼저 찾고 없으면 그 다음으로 멤버변수를 찾는다. 멤버변수도 없으면 오류가 발생한다.

## 생성자
- 규칙
	- 생성자의 이름은 클래스 이름과 같아야 한다.
	- 반환타입이 없으므로 비워둬야 한다.
	- 나머지는 메서드와 동일
- 인스턴스 생성 후 즉시 호출된다. 
	- new 키워드 이후 `()`는 생성자 호출을 의미한다.
- 생성자 덕분에 자동 초기화로 인한 더미 데이터 생성을 방지하여 초기화를 강제할 수 있다.
- 기본 생성자
	```java
	public class MemberInit {
	
		// 기본 생성자
	    public MemberInit() {
	    }
	}
	```
	- 매개 변수가 없는 생성자
	- 따로 정의한 생성자가 없는 경우 자바 컴파일러가 매개변수와 코드가 없는 기본생성자를 자동으로 만들어 준다.
- 생성자 오버로딩
	- 생성자도 메서드 오버로딩처럼 여러 생성자 제공 가능
	```java
	public class MemberConstruct {
	    String name;
	
	    int age;
	    int grade;
	
		//추가  
		MemberConstruct(String name, int age) {
	         this.name = name;
	         this.age = age;
	         this.grade = 50;
		}
	
		MemberConstruct(String name, int age, int grade) {  
	         this.name = name;
	         this.age = age;
	         this.grade = grade;
	    }
	}
	```
- `this()`
	- 생성자 내부에서 자신의 생성자를 호출할 수 있다. (중복 제거를 위해)
	- 단, **`this()`는 생성자 코드 첫줄에만 작성할 수 있다.** (아니면 컴파일 오류 발생)
	```java
	public class MemberConstruct {
	    String name;
	    int age;
	    int grade;
	
		MemberConstruct(String name, int age) { 
			this(name, age, 50); //변경
		}
	
		MemberConstruct(String name, int age, int grade) {  
	         this.name = name;
	         this.age = age;
	         this.grade = grade;
	     }
	}
	```

## 절차 지향 프로그래밍 VS 객체 지향 프로그래밍
- 절차 지향 프로그래밍
	- 프로그램의 흐름을 순차적으로 따르며 처리하는 방식
	- 데이터와 기능이 분리되어 있다.
	- 데이터와 기능의 분리는 유지보수 관점에서 관리 포인트가 2곳으로 늘어난다.
- 객체 지향 프로그래밍
	- **객체들 간의 상호작용**을 중심으로 프로그래밍하는 방식 (실제 세계의 사물이나 사건을 단순하게 추상화해 속성과 기능을 가진 객체로 본다.)
	- **속성**과 **기능**(메서드)이 객체 안에 함께 포함되어 있다. (캡슐화)
	- 실세계를 **역할**(인터페이스)과 **구현**(구현한 클래스 혹은 객체)으로 구분 (다형성)
	- 장점
		- 객체 사용자의 입장에서 보다 친숙하고 코드가 더 읽기 쉬워진다.
		- 유연하고 변경이 용이하다. (OCP 원칙을 지키는 확장 가능한 설계)
			- 클라이언트 코드를 변경하지 않고 서버의 구현 기능을 변경할 수 있다.
			- (= 클라이언트는 인터페이스만 알면 내부 구조를 몰라도 되고 내부 구조를 변경해도 영향을 받지 않는다.)
	- 한계
		- 인터페이스가 변하면 클라이언트, 서버 모두 큰 변경이 발생한다.
		- 따라서 인터페이스를 안정적으로 잘 설계하는 것이 중요하다.

## 캡슐화(Encapsulation)
- 속성과 기능을 하나로 묶어서 꼭 필요한 기능만 메서드를 통해 외부에 제공하고 나머지는 모두 내부로 숨기는 것
- 속성과 기능 묶기 + 접근 제어자를 통해 실현
- 좋은 캡슐화
	- 속성은 반드시 숨기자.
		- 객체의 데이터는 객체가 제공하는 기능인 메서드를 통해서 접근해야 한다.
		- 데이터를 외부에 열어두면 클래스 내 데이터를 다루는 로직을 무시하고 데이터를 변경할 수 있음
	- 꼭 필요한 기능만 노출하자.
		- 클래스 내부에서만 사용하는 기능들은 모두 감추는 것 좋다.
		- 사용하는 개발자 입장에서 필요한 기능만 정리되어 복잡도가 낮아진다.
- 음악 플레이어 예제

>메소드 추출 팁
>
>자신이 가진 데이터로 계산한다면, 일반적으로 자기자신이 메서드로 계산하는게 좋다.
>나중에 수정이 생기거나 변경이 생길 때 본인만 바꾸면 되므로 관리가 편하다.

## 접근 제어자
- 해당 클래스 외부에서 특정 필드나 메서드에 접근하는 것을 허용하거나 제한할 수 있다.
- 필드, 메서드, 생성자에 사용된다.
	- 지역변수는 스코프 내에서만 사용하므로 접근제어자를 사용하는 의미가 없고 사용할 수도 없다.
- 클래스에는 일부만 사용가능하다. (`public`, `default`)
	- `public` 클래스는 반드시 파일명과 이름이 같아야 한다.
	- 하나의 자바 파일에 `public` 클래스는 하나만, `default` 클래스는 무한정 만들 수 있다.
- 종류
	- `private`: 모든 외부 호출을 막는다.
	- `default(package-private)`: **같은 패키지**안에서 호출은 허용한다.
	- `protected`: default + 다른 패키지어도 **상속 관계**의 호출은 허용한다.
	- `public`: 모든 외부 호출을 허용한다.

## 상속(Inheritance)
- `extends`
- 기존 부모 클래스의 필드와 메서드를 새로운 자식 클래스에서 재사용하는 것
- 중복을 줄이고 편리하게 확장할 수 있음
- 단일 상속만 할 수 있다. (다중 상속은 불가능)
	- 만일, 두 부모를 상속받았는데 둘 다 `move()`라는 메서드를 가지고 있다면 어떤 메서드를 실행해야할지 애매하다. (다이아몬드 문제)
	- 클래스 계층구조가 매우 복잡해질 수 있다.
- 메서드 오버라이딩
	- 상속 받은 기능을 자식이 재정의하는 것
	- 멤버변수는 오버라이딩되지 않는다.
	- `@Override`
- 메모리 구조
	- 상속관계 객체 생성 시 그 내부에 부모와 자식이 모두 생성된다. (하나의 참조값에 두 클래스 정보가 공존)
- **상속관계 호출시 대원칙**
	- *상속관계 객체 호출 시, 호출자의 타입을 기준으로 먼저 찾는다.*
	- *현재 타입에서 기능을 찾지 못하면 상위 부모 타입으로 기능을 찾아서 실행한다.* (끝까지 올라가도 없으면 컴파일 오류 발생)
	- *자식 클래스에 오버라이딩된 메서드가 있다면 항상 우선하여 호출된다.*
- `Car`와 `ElectricCar` 예제

## `super`
- 상속관계에서 부모와 자식의 필드 이름과 메서드 이름이 같은 경우, 부모를 참조하고 싶을 때 `super`를 통해 부모 클래스로 접근한다.
- 생성자
	- 상속관계를 사용하면 자식 클래스의 생성자와 부모 클래스의 생성자를 반드시 호출해야 한다.
	- 상속 시 **생성자 첫 줄**에 `super()`를 사용해 부모 클래스 생성자를 호출해야 한다.
		- 예외로 첫 줄에 `this()`(=나말고 다른 생성자를 호출해줘)를 사용할 수 있다.
		- 그러나 자식 생성자 내에서 언젠간 `super()`가 호출되어야 한다.
	- 부모 클래스의 생성자가 기본생성자라면 `super()`를 생략할 수 있다.
	- 결과적으로 상속관계 생성자 호출은 부모에서 자식 순으로 실행된다.

## 다형성(Polymorphism)
- 다른 타입의 객체를 하나인 것처럼 처리해 주는 것 (아래 두가지 특성 덕분에 실현됨)
- (= 한 객체가 여러 타입의 객체로 취급될 수 있는 것)
- 다형적 참조
	- 부모는 자식을 품을 수 있다. (부모 타입의 변수가 다양한 자식 인스턴스를 참조할 수 있다.)
		- `Parent poly = new Child()`
	- = 업캐스팅 (업캐스팅은 생략이 가능하고 권장된다.)
		- 업캐스팅은 메모리상에 인스턴스가 항상 존재하므로 안전하다.
	- 반면에, 자식은 부모를 품을 수 없다.
		- `Child child = poly // 컴파일 에러`
	- 만약 부모 클래스에서 자식 클래스의 메서드를 호출하고 싶다면 다운캐스팅 해야한다.
		- `Child child = (Child) poly`
		- `((Child) poly).childMehtod()` (일시적 다운 캐스팅도 가능)
		- 다만, 다운캐스팅은 자식 타입이 메모리상에 존재하지 않을 경우 `ClassCastException` 런타임 에러를 발생시키므로 매우 주의가 필요하다.
	- 다운 캐스팅 시 `instance of`를 사용하면 안전하다.
		- 오른쪽에 있는 타입에 왼쪽에 있는 인스턴스 타입이 들어갈 수 있으면 `true`, 아니면 `false`
		- `new Parent() instanceof Parent // true`
		- `new Child() instanceof Parent // true`
		- `new Parent() instanceof Child // false`
		- 자바 16부터는 `instanceof`와 동시에 변수 선언도 가능하다.
			- `if (parent instanceof Child child) {...}`
	- 다형적 참조 덕분에 자식 인스턴스들을 함수의 부모 타입 매개변수로 참조하거나, 배열의 타입을 부모 타입으로 가져가 자식 인스턴스들을 참조할 수 있다. (중복 제거 및 반복 가능)
- 메서드 오버라이딩
	- 오버라이딩된 메서드는 항상 우선권을 가진다.
	- 자식에서도 오버라이딩하고 손자에서도 오버라이딩했다면, 손자의 오버라이딩 메서드가 우선권을 가진다.
	- 만일 메서드 오버라이딩이 없다면 항상 부모 타입의 메서드를 호출했을 것이다.
- **다형성 덕분에 IoC, OCP, DIP, 전략 패턴 등이 가능해짐**
- **다형성이 매우 중요하다.**

## OCP 원칙
- 좋은 객체 지향 설계 원칙 중 하나
- Open for extension, Closed for modification (확장에는 열려있고 변경에는 닫혀 있다)
- 기존의 코드 수정 없이 새로운 기능을 추가할 수 있다는 의미

## 다형성을 보완하는 추상 클래스
- 추상 클래스는 다형성만으로 생기는 두 가지 문제를 해결한다.
	- 부모 클래스를 인스턴스로 생성할 수 있는 문제 (추상적인 개념이 실제로 존재하는 것은 이상함)
	- 부모 클래스를 상속 받는 자식 클래스가 메서드 오버라이딩을 하지 않을 가능성 (개발자의 실수)
- 추상 클래스
	- 부모 클래스는 제공하지만 실제 생성되면 안되는 클래스
	- 추상적인 개념을 제공하며 부모 클래스 역할로서 상속 목적으로 사용
	- **인스턴스를 생성할 수 없음** (제약 1)
	- `abstract class AbstractAnimal {...}`
- 추상 메서드
	- 자식 클래스가 **반드시 오버라이딩해야 하는 메서드** (제약 2)
	- 메서드 바디가 없음
	- 추상 메서드가 하나라도 있는 클래스는 추상 클래스로 선언해야 한다.
	- `public abstract void sound()`

## 인터페이스 - 순수 추상 클래스를 지원
- 인터페이스 등장 배경
	- 추상 클래스는 여전히 자신의 메서드를 가질 수 있다.
	- 반면에, 순수 추상 클래스는 추상 클래스를 실행 로직이 전혀 없는 추상 메서드로만 구성한 것을 의미한다.
	- 이는 **다형성을 위한 규격**, 마치 USB 인터페이스 같은 느낌을 준다.
	- 자바는 이러한 **순수 추상 클래스를 편리하게 사용할 수 있도록 인터페이스**를 지원한다.
- 특징
	- `interface` 키워드, 구현시 `implements` 키워드 사용
	- 인터페이스의 메서드는 모두 `public abstract`이다. (직접 쓸 수도 있지만 생략 권장)
	- 인터페이스의 멤버 변수는 `public static final`이다. (마찬가지로 생략 권장)
	- **구현**이라는 용어 사용
		- 상속은 부모의 기능을 물려 받는 것이지만, 인터페이스는 모든 메서드가 추상 메서드이므로 물려받을 기능이 없고 오히려 자식이 오버라이딩해서 메서드를 구현해야 한다.
		- 다만, 자바 입장에서는 상속이나 구현이나 동일하게 동작한다.
		- 클래스 & 추상 클래스 & 인터페이스는 코드와 메모리 구조상 모두 동일하다.
	- **다중 구현**을 지원
- 유용한 이유
	- 제약
		- 인터페이스의 메서드를 반드시 구현하라는 규약을 준다.
		- 순수 추상 클래스를 지향해도 추상 클래스는 다른 개발자가 미래에 메서드를 추가할 수 있기 때문에, 인터페이스는 이를 예방한다.
	- 다중 구현
		- 클래스의 상속이 하나의 부모만 지정할 수 있는 것과 달리, 인터페이스는 여러 부모를 둘 수 있다.
		- 인터페이스는 자신이 구현을 가지지 않고, 자식이 메서드를 구현한다. 또한 어차피 오버라이딩으로 인해 자식의 메서드가 호출된다. 따라서, 다이아몬드 문제가 발생하지 않는다.


>의존 관련 용어 정리
>
>A -> B (UML)
>= A가 B를 안다.
>= A가 B를 의존한다.
>= A가 B를 상속받았다.

***
## Reference
*김영한의 실전 자바 - 기본편*