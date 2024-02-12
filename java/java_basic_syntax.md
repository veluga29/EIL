# 자바 주요 syntax 정리
## 대원칙
- 자바는 항상 변수의 값을 복사해서 대입한다.

## 변수 선언과 초기화
* 변수 선언
	* 메모리 공간을 확보해서 데이터를 저장할 그릇을 만드는 것
	* 변수 이름을 통해 해당 메모리공간에 접근한다.
* 변수 초기화
	* 선언한 변수에 처음으로 값을 저장하는 것
	* 초기화하지 않고 사용할 경우 컴파일 에러
		* 변수를 선언하면 어떤 메모리 공간을 차지하지만, 해당 공간은 다른 프로그램이 사용하고 종료되어 남겨진 알 수 없는 값이 있을 수 있다. 
		* 따라서, 초기화하지 않으면 이상한 값이 출력될 수 있으므로, 자바는 문제를 예방하기 위해 변수 접근 전에 초기화를 강제
* 선언 및 초기화 방식 예제
	```java
	// 1. 변수 선언, 초기화 각각 따로  
	int a;  
	a = 1;  
	System.out.println(a);  
	  
	// 2. 변수 선언과 초기화를 한번에  
	int b = 2;  
	System.out.println(b);  
	  
	// 3. 여러 변수 선언과 초기화를 한번에  
	int c = 3, d = 4;  
	System.out.println(c);  
	System.out.println(d);  
	  
	// 4. 여러 변수 선언을 한번에  
	int e, f;
	```

>컴파일 에러 & 런타임 에러
>
>컴파일 에러는 문법에 맞지 않았을 때 발생하는 에러로 오류를 빠르고 명확하게 찾을 수 있어서 좋은 에러이다.
>반면에, 런타임 에러는 프로그램 실행 시 발생하는 에러로 미리 예방이 어려워 나쁜 에러이다. 예를 들어, 고객이 계좌이체를 했는데 내 돈은 나가고 상대방에게 돈이 안들어간 경우 돈이 증발되는 비극이 런타임에서 발생할 수 있다.

## 주로 사용하는 변수 타입
* 정수
	* `int` (기본)
	* `long` (리터럴 값이 20억이 넘을 것 같을 때 사용)
* 실수
	* `double`
* 불린형
	* `boolean`
* 문자열
	* `String`
		* 문자 하나든 문자열이든 모두 String을 사용하는 것이 편리하다.
		* 문자 길이에 따라 메모리 공간이 동적으로 달라짐
* 예외: 파일 다루기
	* `byte` (파일은 바이트 단위로 다루므로 파일 전송, 파일 복사 등에 주로 사용)

## 변수 명명 핵심 관례
* 클래스는 대문자로 시작, 나머지는 소문자로 시작
* 예외
	* 상수는 모두 대문자를 사용하고 언더바로 구분
	* 패키지는 모두 소문자로 사용

## 전위 & 후위 증감 연산자
* 전위 증감 연산자
	* 증감 연산이 먼저 수행된 후 나머지 연산 수행
* 후위 증감 연산자
	* 다른 연산이 먼저 수행된 후 증감 연산 수행
* 예제
	```java
	public class OperatorAdd {  
	    public static void main(String[] args) {  
	        // 전위 증감연산자  
	        int a = 1;  
	        int b = 0;  
	        b = ++a;  
	  
	        System.out.println("a = " + a + ", b = " + b); // a = 2, b = 2  
	  
	        // 후위 증감연산자  
	        a = 1;  
	        b = 0;  
	        b = a++;  
	  
	        System.out.println("a = " + a + ", b = " + b); // a = 2, b = 1  
	    }  
	}
	```

## 새로운 switch syntax
자바 14부터 조금 더 깔끔한 switch 문법이 도입되었다.
```java
public class Switch {  
    public static void main(String[] args) {  
        int grade = 2;  
  
        int coupon = switch (grade) {  
            case 1 -> 1000;  
            case 2 -> 2000;  
            case 3 -> 3000;  
            default -> 500;  
        };  
        System.out.println("발급받은 쿠폰 " + coupon);  
    }  
}
```

## 삼항 연산자 syntax 예제
```java
String coffee = (time < 3) ? "caffeine" : "decaffeine";
```

## do-while문
조건에 상관없이 무조건 한 번은 코드를 실행한다.
```java
public class DoWhile {  
    public static void main(String[] args) {  
        int i = 5;  
  
        do {  
            System.out.println(i);  
            i++;  
        } while (i < 3);  
    }  
}
```

## 무한 루프 For문
For문은 초기식, 조건식, 증감식의 생략이 가능하고 모두 생략할 경우 무한 루프 반복문이 된다.
(= while true)
```java
public class ForLoopInfinite {  
    public static void main(String[] args) {  
        int i = 1;  
        for (;;) {  
            if (i >= 10) {  
                System.out.println("Exit on 10");  
                break;  
            }  
            System.out.println(i);  
            i++;  
        }  
    }  
}
```
다음과 같이 특정식만 생략하는 경우도 가능하다. 결과는 위와 동일하다.
```java
public class ForLoopInfinite {  
    public static void main(String[] args) {  
        for (int i = 1; ; i++) {  
            if (i >= 5) {  
                System.out.println("Exit on 5");  
                break;  
            }  
            System.out.println(i);  
        }  
    }  
}
```

## 지역 변수와 스코프
* 지역 변수(Local Variable)
	* 특정 지역에서만 사용할 수 있는 변수
	* 지역은 코드 블록(`{}`)을 의미
	* 자신이 선언된 코드 블록 안에서만 생존하고, 블록을 벗어나면 제거된다.
* 스코프(Scope)
	* 변수의 접근 가능한 범위
		* for문의 초기식은 for문 내 scope에서만 접근 가능하고 바깥에서는 사용할 수 없다.
	* 변수의 스코프를 꼭 필요한 곳에 한정해 사용해야 메모리를 효율적으로 사용하고 더 유지보수하기 좋은 코드가 된다.
		* main() 코드 스코프의 변수는 프로그램이 종료될 때까지 메모리에 유지되어 비효율적이다. (**비효율적인 메모리 사용**)
		* 필요한 곳 바깥에서부터 변수를 선언하면 생각해야 할 변수가 늘어 복잡하다. (**코드 복잡성 증가**)

## 연산 시 주요 핵심
* 같은 타입끼리의 연산 결과는 타입이 동일하다. (`int` + `int`는 `int`다)
* 서로 다른 타입의 계산은 큰 범위로 자동 형변환이 일어난다. (`int` + `long`은 `long` + `long`)

## 자동 형변환과 명시적 형변환
* 자동 형변환
	* 작은 범위에서 큰 범위로 대입은 허용한다.
		* `int` < `long` < `double`
	* 큰 범위에서작은 범위는 문제가 발생
		* 소수점 버림
		* 오버플로우
	* 다른 타입끼리의 연산 시 큰 범위로 자동 형변환이 발생한다.
* 명시적 형변환
	* 위험을 감수하고 데이터 타입을 강제로 변경하는 것
	* 자바는 기본적으로 큰 범위에서 작은 범위의 대입에 대해 컴파일 에러를 발생시킨다.
		* 은행 이자를 계산하는데 타입 문제로 (double -> int) 이자가 날아가버리는 등의 큰 문제를 방지하기 위해
	* 따라서, 큰 범위에서 작은 범위 대입은 명시적 형변환이 필요하다.
		* `(int) 3.0`
* 형변환 예제
	```java
	public class Casting {  
	    public static void main(String[] args) {  
	        int div1 = 3 / 2;  
	        System.out.println("div1 = " + div1); //1  
	  
	        double div2 = 3 / 2;  
	        System.out.println("div2 = " + div2); //1.0  
	  
	        double div3 = 3.0 / 2;  
	        System.out.println("div3 = " + div3); //1.5  
	  
	        double div4 = (double) 3 / 2;  
	        System.out.println("div4 = " + div4); //1.5  
	  
	        int a = 3;  
	        int b = 2;  
	        double result = (double) a / b;  
	        System.out.println("result = " + result); //1.5  
	    }  
	}
	```

## 데이터 타입 분류
* 기본형(Primitive type)
	* `int`, `long`, `double`, `boolean` 처럼 데이터 값을 변수에 직접 넣을 수 있는 데이터 타입
	* 데이터 사이즈가 정해져 있음
	* 더 빠르고 효율적인 메모리 처리
* 참조형(Reference Type)
	* 데이터에 접근하기 위한 참조(주소)를 저장하는 데이터 타입 (배열, 객체)
	* 생성 시점에서 동적 메모리 할당
	* 유연성 있고 더 복잡한 데이터 구조를 다룰 수 있음

## 배열
* 따로 초기화 하지 않는 경우, 배열 생성시 내부 값이 자동으로 초기화된다.
	* 숫자는 `0`, 불린형은 `false`, 문자열은 `null`
* 배열 변수는 실제 배열이 존재하는 메모리 공간에 대한 주소(참조값)를 담는다. 
* 배열 초기화 예제
	```java
	public class ArrayInitialization {  
		public static void main(String[] args) {  
			// 기본 선언 및 배열 생성  
			int[] students1;  
			students1 = new int[5];  
	  
			// 선언 & 초기화  
			int[] students2 = new int[]{1, 2, 3, 4, 5};  
	  
			// 선언 & 더 간단한 초기화  
			int[] students3 = {1, 2, 3, 4, 5};  
	  
			// 오류 케이스  
	//        int[] students4;  
	//        students4 = {1, 2, 3, 4, 5};  
		}  
	}
		```

## For-Each문(Enhanced For Loop)
컬렉션(배열, set etc...)의 요소를 탐색할 때 조금 더 편리한 기능을 제공하는 문법이다.
컬렉션 요소들을 처음부터 끝까지 탐색한다.
```java
public class EnhancedForLoop {  
    public static void main(String[] args) {  
        int[] numbers = {1, 2, 3, 4, 5};  
        for (int number : numbers) {  
            System.out.println(number);  
        }  
    }  
}
```

## 메서드(Method) 유의점
* 자바는 항상 변수의 값을 복사해서 대입한다. (인자가 파라미터로 전달될 때도 마찬가지다.)
* 메서드 호출이 끝나면 메서드 내 파라미터 변수, 로컬 변수들이 모두 메모리에서 제거된다.
* 메서드 호출 시에도 인자의 타입이 메서드 파라미터의 타입과 똑같아야 하므로, 명시적 형변환이 필요하거나 자동 형변환이 일어날 수 있다.

## 메서드 오버로딩(Method Overloading)
이름이 같고 매개변수가 다른 메서드를 여러개 정의하는 것을 의미한다.
* 메서드 시그니처(method signature)
	* 자바에서 메서드를 구분할 수 있는 고유한 식별자
	* 메서드 시그니처 = 메서드 이름 + 매개변수 타입(순서)
	* 반환 타입은 시그니처에 포함되지 않는다.
* 규칙
	* 메서드의 이름이 같아도 매개변수의 타입 및 순서가 다르면 오버로딩할 수 있다. (=메서드 시그니처가 달라서 가능)
	* 반환 타입은 인정하지 않는다.
	* 여러 개의 메서드를 오버로딩했을 때, 자바는 먼저 본인의 타입에 최대한 맞는 메서드를 찾아 실행하고, 그래도 없으면 형변환 가능한 타입의 메서드를 찾아 실행한다.  

>메서드 오버로딩 유의 케이스
>
>아래 두 가지는 메서드 오버로딩 하지 못한다.
>메서드 시그니처가 같기 때문!
>`int add(int a, int b)`
>`int add(int c, int d)`

___
## Reference
*[김영한의 자바 입문 - 코드로 시작하는 자바 첫걸음](https://www.inflearn.com/course/%EA%B9%80%EC%98%81%ED%95%9C%EC%9D%98-%EC%9E%90%EB%B0%94-%EC%9E%85%EB%AC%B8)*