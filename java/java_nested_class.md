## 중첩 클래스의 분류
![java nested class classification](../images/java_nested_class_classification.png)
- **클래스를 정의하는 위치**에 따라 총 4가지, 크게 2가지로 분류 (변수 선언 위치와 동일)
	- **정적 중첩 클래스** (정적 변수와 같은 위치)
	- 내부 클래스
		- **내부 클래스** (인스턴스 변수와 같은 위치)
		- **지역 클래스** (지역 변수와 같은 위치)
		- **익명 클래스** (지역 클래스의 특별 버전)
- 중첩 클래스
	- 정적 중첩 클래스 (Nested)
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
			- `new 바깥클래스.중첩클래스()`
			- 바깥 클래스 생성과 상관없이 **단독 생성 가능**
			- 물론, 정적 중첩 클래스는 `private`으로 두고 **바깥 접근 및 생성을 막는게 옳음**
				- 중첩 클래스는 용도가 소속된 바깥 클래스의 내부에서 사용되는 것이므로
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
	- 내부 클래스 (Inner)
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
			- 개념상 바깥 인스턴스 안에 내부 인스턴스가 생성
				- 내부 인스턴스가 참조값을 가지므로
		- **바깥 클래스의 인스턴스 변수 접근 가능** (클래스 변수도 접근 가능)
			- 참조값을 가짐
		- `private` 접근 제어자 관점
			- 바깥 클래스와 내부 클래스는 **서로의 `private` 접근 제어자에 접근 가능**
		- 생성 방법
			- `바깥클래스의 인스턴스 참조.new 내부클래스()`
			- **바깥 클래스의 인스턴스**를 **먼저 생성**해야 내부 클래스의 인스턴스를 생성 가능
		- 종류
			- 내부 클래스: 바깥 클래스의 **인스턴스 멤버에 접근**
			- 지역 클래스: 내부 클래스의 특징 + 지역 변수에 접근
			- 익명 클래스: 지역 클래스의 특징 + 클래스 이름 X
- 중첩 클래스 사용 상황 => 패키지 내 다량의 클래스들 속에서 **개발자의 혼란을 줄임**
	- 특정 클래스가 다른 하나의 클래스 **안에서만 사용됨**
	- 둘이 아주 **긴밀하게 연결**되어 있는 경우
- 장점
	- **논리적 그룹화**
	- **캡슐화**
		- 다른 곳에서 사용될 필요 없는 **중첩 클래스가 외부에 노출 X**
		- **중첩 클래스**는 **바깥 클래스의 `private` 멤버에 접근 가능**
			- **불필요한 `public` 제거** 및 **긴밀한 연결**