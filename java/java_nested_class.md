## 중첩 클래스의 분류
![java nested class classification](../images/java_nested_class_classification.png)
- **클래스를 정의하는 위치**에 따라 총 4가지, 크게 2가지로 분류 (변수 선언 위치와 동일)
	- **정적 중첩 클래스** (정적 변수와 같은 위치)
	- 내부 클래스
		- **내부 클래스** (인스턴스 변수와 같은 위치)
		- **지역 클래스** (지역 변수와 같은 위치)
		- **익명 클래스** (지역 클래스의 특별 버전)
- 중첩 클래스 사용 상황 => 패키지 내 꼭 필요한 클래스들만 노출시켜 **개발자의 혼란을 줄임**
	- 특정 클래스가 다른 하나의 클래스 **안에서만 사용됨** (다른 클래스에서 사용시 중첩 클래스 사용 X)
	- 둘이 아주 **긴밀하게 연결**되어 있는 경우
- 장점
	- **논리적 그룹화**
	- **캡슐화**
		- 다른 곳에서 사용될 필요 없는 **중첩 클래스가 외부에 노출 X**
		- **중첩 클래스**는 **바깥 클래스의 `private` 멤버에 접근 가능**
			- **불필요한 `public` 제거** 및 **긴밀한 연결**
## 정적 중첩 클래스 (Nested)
![java_static_nested_class](../images/java_static_nested_class.png)
```java
public class NestedOuter {

	private static int outClassValue = 3;
	private int outInstanceValue = 2;
	
	static class Nested {
		private int nestedInstanceValue = 1;

		public void print() { 
			
			// 자신의 멤버에 접근
			System.out.println(nestedInstanceValue); 
			
			// 바깥 클래스의 인스턴스 멤버에는 접근할 수 없다.
			// System.out.println(outInstanceValue);

			// 바깥 클래스의 클래스 멤버에는 접근할 수 있다. private도 접근 가능
			System.out.println(NestedOuter.outClassValue);
			System.out.println(outClassValue);
		}
}
```
```java
public class NestedOuterMain {
	public static void main(String[] args) {
		//단독 생성 가능
		NestedOuter.Nested nested = new NestedOuter.Nested(); 
		nested.print();
	}
}
```
- **`static` 키워드 O**
- 바깥 클래스의 **인스턴스에 소속 X**
	- 바깥 클래스의 **내부**에 있지만, 바깥 클래스와 **관계 없는 전혀 다른 클래스** (내 것이 아닌 것)
	- 단지 구조상 중첩해뒀을 뿐 클래스 2개와 큰 차이 없음
		```java
		class NestedOuter {
		}
		
		class Nested {
		}
		```
- **바깥 클래스의 인스턴스 변수 접근 불가** (클래스 변수는 접근 가능)
	- 바깥 인스턴스의 **참조값이 없기 때문**
- `private` 접근 제어자 관점
	- 바깥 클래스와 중첩 클래스는 **서로의 `private` 접근 제어자에 접근 가능**
	- 둘은 **접근제어자 관점에서 한 식구**
- 생성 방법
	- 바깥 클래스의 바깥에서 접근 및 생성
		- 접근: `바깥클래스.중첩클래스`
		- 생성: `new 바깥클래스.중첩클래스()`
		- 바깥 클래스 생성과 상관없이 **단독 생성 가능**
	- **바깥 클래스의 내부에서 접근 및 생성** (**권장**)
		- 접근: `중첩클래스`
		- 생성: `new 중첩클래스()`
		- 정적 중첩 클래스는 `private`으로 두고 **바깥 접근 및 생성을 막는게 옳음**
		- 중첩 클래스의 **용도**는 소속된 바깥 클래스의 **내부에서 사용**되는 것이므로
		```java
		public class Network {
			 
			public void sendMessage(String text) {
				NetworkMessage networkMessage = new NetworkMessage(text);
			}
			
			private static class NetworkMessage {
				...
			}
		}
		```
## 내부 클래스 (Inner)
![java_nested_inner_class_logical](../images/java_nested_inner_class_logical.png)
![java_nested_inner_class_physical](../images/java_nested_inner_class_physical.png)
```java
public class InnerOuter {
	
	private static int outClassValue = 3;
	private int outInstanceValue = 2;
	
	class Inner {
		
		private int innerInstanceValue = 1;
		
		public void print() {
			// 자신의 멤버에 접근 
			System.out.println(innerInstanceValue);
			
			// 외부 클래스의 인스턴스 멤버에 접근 가능, private도 접근 가능
			System.out.println(outInstanceValue);
			
			// 외부 클래스의 클래스 멤버에 접근 가능. private도 접근 가능
			System.out.println(InnerOuter.outClassValue);
		} 
	}
}
```
```java
public class InnerOuterMain {
	 
	 public static void main(String[] args) {
		 InnerOuter outer = new InnerOuter();
		 InnerOuter.Inner inner = outer.new Inner();
		 inner.print();
	 }
}
```
- **`static` 키워드 X**
- 바깥 클래스의 **인스턴스에 소속 O**
	- 바깥 클래스의 **내부**에 있으면서, 바깥 클래스를 **구성하는 요소** (나를 구성하는 요소)
	- **개념**상으로는 바깥 인스턴스 안에 내부 인스턴스가 생성
	- **실제**로는 내부 인스턴스가 **바깥 인스턴스의 참조값 보관**
- **바깥 클래스의 인스턴스 변수 접근 가능** (클래스 변수도 접근 가능)
	- 참조값을 가짐
- `private` 접근 제어자 관점
	- 바깥 클래스와 내부 클래스는 **서로의 `private` 접근 제어자에 접근 가능**
- 생성 방법
	- 바깥 클래스의 바깥에서 생성
		- 생성: `바깥클래스의 인스턴스 참조.new 내부클래스()`
		- **바깥 클래스의 인스턴스**를 **먼저 생성**해야 내부 클래스의 인스턴스 생성 가능
	- **바깥 클래스의 내부에서 생성** (**권장**)
		- 생성: `new 내부 클래스()`
		- **내부 클래스의 인스턴스**는 자신을 생성한 **바깥 클래스의 인스턴스**를 **자동으로 참조**
		```java
		public class Car {
			
			private String model;
			private int chargeLevel;
			private Engine engine;
			
			public Car(String model, int chargeLevel) {
				this.model = model;
				this.chargeLevel = chargeLevel;
				this.engine = new Engine();
			}
			
			public void start() {
				engine.start();
				System.out.println(model + " 시작 완료"); 
			}
			
			private class Engine {
				public void start() {
					System.out.println("충전 레벨 확인: " + chargeLevel);
					System.out.println(model + "의 엔진을 구동합니다."); }
				}
			}
			
		}
		```
- 종류
	- 내부 클래스: 바깥 클래스의 **인스턴스 멤버에 접근**
	- 지역 클래스: 내부 클래스의 특징 + 지역 변수에 접근
	- 익명 클래스: 지역 클래스의 특징 + 클래스 이름 X
## 섀도잉 (Shadowing)
```java
// 내부 클래스 예시
public class ShadowingMain {
    
    public int value = 1;
    
    class Inner {
        public int value = 2;
        
        void go() {
            int value = 3;
            System.out.println("value = " + value); // 3
            System.out.println("this.value = " + this.value); // 2
            System.out.println("ShadowingMain.value = " + ShadowingMain.this.value); // 1
        }
	}
    
    public static void main(String[] args) {
        ShadowingMain main = new ShadowingMain();
        Inner inner = main.new Inner();
        inner.go();
    }
}
```
- **바깥 클래스의 인스턴스 변수 이름**과 **내부 클래스의 인스턴스 변수 이름**이 **같다면?**
- 변수 이름이 같을 때 프로그래밍에서는 대부분 **더 가깝거나 구체적인 것이 우선권을 가짐**
- **새도잉**: 다른 변수들을 가려서 보이지 않게 하는 것
	- value = 3
	- this.value = 2
	- ShadowingMain.value = 1
- 다른 변수를 가리더라도 **인스턴스 참조**를 사용해 외부 변수 접근 가능
	- 내부 클래스 인스턴스 접근: `this`
	- 바깥 클래스 인스턴스 접근: `바깥클래스이름.this`
- 물론, 이름이 같은 경우 **처음부터 이름을 서로 다르게 지어서 명확하게 구분**하는 것이 **더 나은 방법**