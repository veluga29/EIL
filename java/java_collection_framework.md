## 자바 컬렉션 프레임 워크
![java_collection_framework_overview](../images/java_collection_framework_overview.png)
- 자바는 컬렉션 프레임워크를 통해 **다양한 자료구조**를 **인터페이스, 구현, 알고리즘**으로 지원
- 데이터 컬렉션을 **효율적으로 저장하고 처리**하기 위한 **통합 아키텍처** 제공
- **핵심 인터페이스**
	- `Collection`
		- **단일 루트 인터페이스**로 모든 컬렉션 클래스가 상속 받음
		- 필요성
			- **가장 기본적인 인터페이스**로 다양한 컬렉션 타입이 공통적으로 따라야하는 **기본 규약** 정의
			- 이같은 설계는 **일관성**, **재사용성**, **확장성** 향상시키고 **다형성** 이점 제공
		- 주요 메서드
			- `add(E e)` : 컬렉션에 요소를 추가
			- `remove(Object o)` : 주어진 객체를 컬렉션에서 제거
			- `size()` : 컬렉션에 포함된 요소의 수를 반환
			- `isEmpty()` : 컬렉션이 비어 있는지 확인
			- `contains(Object o)` : 컬렉션이 특정 요소를 포함하고 있는지 확인
			- `iterator()` : 컬렉션의 요소에 접근하기 위한 반복자를 반환
			- `clear()` : 컬렉션의 모든 요소를 제거
	- `List`
		- **순서**가 있는 컬렉션
		- **중복 O**
		- **인덱스** 통한 요소 **접근 O**
		- 구현
			- **`ArrayList`**(주로 사용): 내부적으로 **배열** 사용
			- `LinkedList`: 내부적으로 **연결 리스트** 사용
	- `Set`
		- **중복을 허용하지 않는** 컬렉션
		- **인덱스** 통한 요소 **접근 X**
		- 구현
			- **`HashSet`**(주로 사용): 내부적으로 **해시 테이블** 사용
			- `LinkedHashSet`: 내부적으로 **해시 테이블**과 **연결리스트** 사용
			- `TreeSet`: 내부적으로 **레드-블랙 트리** 사용
	- `Queue`
		- **요소 처리 전 보관**하는 컬렉션
		- 구현
			- **`ArrayDeque`**(주로 사용): 내부적으로 **배열 기반 원형 큐** 사용 (대부분의 경우 **빠름**)
			- `LinkedList`: 내부적으로 **연결리스트** 사용
			- `PriorityQueue`
	- `Map` (**`Collection` 상속 X**)
		- **키와 값 쌍**으로 요소를 **저장**하는 객체
		- 구현
			- **`HashMap`**(주로 사용): 내부적으로 **해시 테이블** 사용
			- `LinkedHashMap`: 내부적으로 **해시 테이블**과 **연결리스트** 사용
			- `TreeMap`: 내부적으로 **레드-블랙 트리** 사용
- **알고리즘**
	- 컬렉션 프레임워크는 데이터 처리 및 조작 알고리즘 제공 (**정렬, 검색, 순환, 변환** 등)
	- 제공 방법
		- 자료구조 **자체적으로 기능** 제공
		- **`Collections`** 와 **`Arrays`** 클래스에 **정적 메서드 형태**로 구현
- **실무 선택 전략**
	- **순서가 중요 O**, **중복 허용 O** 경우: **`List`** 인터페이스를 사용
		- **`ArrayList`** 선택 (**주로 사용**)
		- 추가/삭제 작업이 앞쪽에서 빈번하다면 `LinkedList` (성능상 더 좋은 선택)
	- **중복 허용 X** 경우: **`Set`** 인터페이스 사용
		- 순서가 중요하지 않다면 **`HashSet`** (**주로 사용**)
		- 순서를 유지해야 하면 `LinkedHashSet`
		- 정렬된 순서가 필요하면 `TreeSet`
	- **요소를 키-값 쌍으로 저장**하려는 경우: **`Map`** 인터페이스를 사용
		- 순서가 중요하지 않다면 **`HashMap`** (**주로 사용**)
		- 순서를 유지해야 한다면 `LinkedHashMap`
		- 정렬된 순서가 필요하면 `TreeMap`
	- **요소를 처리하기 전에 보관**해야 하는 경우: **`Queue`** , **`Deque`** 인터페이스를 사용
		- **`ArrayDeque`** 선택 (**주로 사용**, 스택/큐 구조 모두에서 **가장 빠름**)
		- 우선순위에 따라 요소를 처리해야 한다면 `PriorityQueue`
## 배열 (Array)
![java_array](../images/java_array.png)
- **순서**가 있고 **중복을 허용**하면서 **크기가 정적으로 고정**된 자료구조
- 가장 **기본**적인 자료구조
- 특징
	- 데이터가 **메모리 상에 순서대로 붙어서 존재**
	- **검색**은 배열의 데이터를 **하나하나 확인**해야해서 **한번에 찾을 수 없음**
- 장점
	- **인덱스** 사용 시 **최고의 효율**
		- 데이터가 아무리 많아도 인덱스는 **한번의 계산**으로 **빠르게 자료 위치 찾음**
		- 공식: **`배열의 시작 참조 + (자료의 크기 * 인덱스 위치)`**
			- `arr[0]: x100 + (4byte * 0): x100`
			- `arr[1]: x100 + (4byte * 1): x104`
			- `arr[2]: x100 + (4byte * 2): x108`
- 단점
	- **배열의 크기**가 생성하는 시점에 **정적**으로 정해짐
		- 처음부터 많이 확보하면 **메모리 낭비**
	- **데이터 추가**가 **불편**
		- **기존 데이터**가 오른쪽으로 한 칸씩 **이동**해야 함
- 시간 복잡도
	- 데이터 추가
		- 앞, 중간에 추가: O(N)
		- **마지막**에 추가: **O(1)**
	- 데이터 삭제
		- 앞, 중간에 삭제: O(N)
		- **마지막**에 삭제: **O(1)**
	- 인덱스 조회, 입력, 변경: **O(1)**
	- 데이터 검색: O(N)
## 리스트 (List)
- **순서**가 있고 **중복을 허용**하면서 **크기가 동적**으로 변하는 자료구조
- 장점
	- 동적으로 데이터를 추가할 수 있는 자료 구조
### 배열 리스트 (ArrayList)
- 데이터를 내부의 **배열**에 보관하는 리스트 구현체
- 특징
	- **데이터 추가**시 **배열의 크기를 초과**할 때마다 **더 큰 크기의 배열**을 새로 **생성**해 **값 복사** 후 사용
		- 복사 전 기존 배열은 GC 대상
		- **보통 50% 증가** 사용
			- 추가할 때마다 만들면 **배열 복사 연산이 너무 많음**
			- 배열의 크기를 너무 크게 증가하면 **메모리 낭비 발생**
	- 추가/삭제 시 **인덱스로 위치 조회**는 **빠르**지만 **추가/삭제 작업 자체**는 **느림**
		- 인덱스로 위치 찾기: **O(1)**
		- 추가/삭제 작업: O(N) - **데이터 이동** 때문에
- 장점
	- **조회**가 **빠름**
	- **끝** 부분에 데이터 **추가 및 삭제** 작업 **빠름**
- 단점
	- **앞, 중간** 부분 데이터 **추가 및 삭제** 작업 **느림** (**데이터 이동**으로 인한 **성능 저하**)
	- 배열 뒷 부분에 **낭비되는 메모리**가 존재
- 시간 복잡도
	- 데이터 추가: O(N)
		- 앞, 중간에 추가: O(N)
		- **마지막**에 추가: **O(1)**
	- 데이터 삭제: O(N)
		- 앞, 중간에 삭제: O(N)
		- **마지막**에 삭제: **O(1)**
	- 인덱스 **조회**: **O(1)**
	- 데이터 검색: O(N)
- 예시 구현
	```java
	public class MyArrayList<E> {
	    
	    private static final int DEFAULT_CAPACITY = 5;
	    private Object[] elementData;
	    private int size = 0;
	    
	    public MyArrayList() {
	        elementData = new Object[DEFAULT_CAPACITY];
		}
	    
	    public MyArrayList(int initialCapacity) {
	        elementData = new Object[initialCapacity];
		}
		
	    public int size() {
	        return size;
		}
		
	    public void add(E e) {
	        if (size == elementData.length) {
				grow(); 
			}
	        elementData[size] = e;
			size++; 
		}
		
	    public void add(int index, E e) {
	        if (size == elementData.length) {
				grow();
			}
	        shiftRightFrom(index);
	        elementData[index] = e;
	        size++;
		}
		
		//요소의 마지막부터 index까지 오른쪽으로 밀기 
		private void shiftRightFrom(int index) { 
			for (int i = size; i > index; i--) {
				elementData[i] = elementData[i - 1];
			}
		}
		
		@SuppressWarnings("unchecked")
		public E get(int index) {
		    return (E) elementData[index];
		}
		
		public E set(int index, E element) {
		    E oldValue = get(index);
		    elementData[index] = element;
		    return oldValue;
		}
		
		public E remove(int index) {
		    E oldValue = get(index);
		    shiftLeftFrom(index);
		    size--;
		    elementData[size] = null;
		    return oldValue;
		}
		
		//요소의 index부터 마지막까지 왼쪽으로 밀기 
		private void shiftLeftFrom(int index) {
		    for (int i = index; i < size - 1; i++) {
		        elementData[i] = elementData[i + 1];
			}
		}
	
		public int indexOf(E o) {
		    for (int i = 0; i < size; i++) {
		        if (o.equals(elementData[i])) {
		            return i;
				}
			}
			return -1;
		}
		
		private void grow() {
		    int oldCapacity = elementData.length;
		    int newCapacity = oldCapacity * 2;
		    elementData = Arrays.copyOf(elementData, newCapacity);
	    }
	    
	    @Override
	    public String toString() {
	        return Arrays.toString(Arrays.copyOf(elementData, size)) + " size=" +
	size + ", capacity=" + elementData.length;
		}
	
	}
	```
### 연결 리스트 (LinkedList)
![java_linked_list](../images/java_linked_list.png)
- 노드를 만들어 **각 노드끼리 서로 연결**하는 리스트 구현체
- 특징
	- 노드와 링크로 구성
	- 추가/삭제 시 **인덱스로 위치 조회**는 **느리**지만 **추가/삭제 작업 자체**는 **빠름**
		- 인덱스로 위치 찾기: O(N) - 데이터 **탐색** 때문
		- 추가/삭제 작업: **O(1)** - 필요한 노드끼리 **참조만 변경**하면 끝
- 장점
	- **앞** 부분 데이터 **추가 및 삭제** 작업 **빠름**
	- **필요한만큼만 동적으로** 노드를 **생성 및 연결**하므로 **메모리 낭비 X**
		- 다만 크게 봤을 때 **배열에 비해 메모리가 엄청 절약 X** (연결 유지 위한 추가 메모리 사용, `next`)
- 단점
	- **중간, 끝** 부분 데이터 **추가 및 삭제** 작업 **느림**
- 시간 복잡도
	- 데이터 추가: O(N)
		- **앞**에 추가: **O(1)**
		- 중간, 마지막에 추가: O(N)
	- 데이터 삭제: O(N)
		- **앞**에 삭제: **O(1)**
		- 중간, 마지막에 삭제: O(N)
	- 인덱스 조회: O(N)
	- 데이터 검색: O(N)
- 예시 구현
	```java
	public class MyLinkedList<E> {
	     
	     private Node<E> first;
	     private int size = 0;
	     
	     public void add(E e) {
	        Node<E> newNode = new Node<>(e);
	        if (first == null) {
	            first = newNode;
	        } else {
		        Node<E> lastNode = getLastNode();
		        lastNode.next = newNode;
		    }
			size++;
		}
		
		private Node<E> getLastNode() {
		    Node<E> x = first;
		    while (x.next != null) {
				x = x.next;
			}
			return x;
		}
		
		public void add(int index, E e) {
		    Node<E> newNode = new Node<>(e);
		    if (index == 0) {
		        newNode.next = first;
		        first = newNode;
		    } else {
		        Node<E> prev = getNode(index - 1);
		        newNode.next = prev.next;
		        prev.next = newNode;
			}
			size++;
		}
		
		public E set(int index, E element) {
		    Node<E> x = getNode(index);
		    E oldValue = x.item;
		    x.item = element;
		    return oldValue;
		}
		
		public E remove(int index) {
		    Node<E> removeNode = getNode(index);
		    E removedItem = removeNode.item;
		    if (index == 0) {
		        first = removeNode.next;
		    } else {
		        Node<E> prev = getNode(index - 1);
		        prev.next = removeNode.next;
		    }
		    removeNode.item = null;
		    removeNode.next = null;
		    size--;
		    return removedItem;
		}
		
		public E get(int index) {
		    Node<E> node = getNode(index);
		    return node.item;
		}
		
		private Node<E> getNode(int index) {
		    Node<E> x = first;
		    for (int i = 0; i < index; i++) {
				x = x.next;
			}
			return x;
		}
		
		public int indexOf(E o) {
		    int index = 0;
		    for (Node<E> x = first; x != null; x = x.next) {
		        if (o.equals(x.item))
		            return index;
		        index++;
			}
			return -1;
		}
		
		public int size() {
		    return size;
		}
		
		@Override
		public String toString() {
		    return "MyLinkedList{" +
		            "first=" + first +
		            ", size=" + size +
					'}';
		}
		
		private static class Node<E> {
		    E item;
		    Node<E> next;
	        
	        public Node(E item) {
	            this.item = item;
			}
			
	        @Override // 가독성 위해 직접 구현 e.g. [A->B->C]
	        public String toString() {
	            StringBuilder sb = new StringBuilder();
	            Node<E> temp = this;
	            sb.append("[");
	            while (temp != null) {
	                sb.append(temp.item);
	                if (temp.next != null) {
	                    sb.append("->");
	                }
	                temp = temp.next;
	            }
	            sb.append("]");
	            return sb.toString();
	        }
		}
	
	}
	```

>**자료구조**와 **제네릭**
>
>일반적으로 **하나의 자료구조**에는 **같은 데이터 타입을 보관하고 관리**한다. 숫자와 문자처럼 관계 없는 여러 데이터 타입을 섞어 보관하는 일은 거의 없다.
>따라서, 자료구조에 **제네릭을 사용**하면 **타입 안정성**이 높은 자료구조를 만들 수 있어 **매우 어울린다**.
>
>만약 배열을 사용하는 경우, 제네릭을 적용해도 **내부 배열의 타입**은 **`Object[] elementData`을 사용**할 것이다. 문제는 생성자 코드에서 배열을 생성할 때이다.
>
>`new E[DEFAULT_CAPACITY]`
>
>제네릭은 타입 매개변수에 의한 `new`를 허용하지 않는다. 또한, 런타임에 타입 정보가 필요한 생성자에서 타입 매개변수를 사용할 수 없다. 이런 **제네릭의 한계**로 인해 **`Object[]` 타입 배열**을 적용하고 **생성자**에서 다음 코드를 사용해야 한다.
>
>**`new Object[DEFAULT_CAPACITY]`**
>
>`Object[]` 타입 적용은 결국 **자료구조 내부에서 다운캐스팅을 사용**하게되는데, **큰 문제는 없다**.
>`Object` 자체는 모든 데이터를 담을 수 있어 신경쓸게 없으니, **조회하는 부분에 초점**을 맞춰보자.
>자료를 입력하는 **`add(E e)` 메서드**에서 **E 타입만 보관**하는 덕분에, **`get()` 메서드**에서 데이터를 조회 후 **`(E)`로 다운캐스팅**해 반환해도 **전혀 문제가 없다.**

>이중 연결 리스트
>
>노드 앞뒤로 연결하는 이중 연결 리스트는 **성능을 더 개선**할 수 있다.
>특히, **자바가 제공하는 연결 리스트**도 **이중 연결 리스트**다. **마지막 노드를 참조하는 변수**를 가지고 있어서, **뒤**에 **추가하거나 삭제**하는 경우에도 **O(1)** 성능을 제공한다.

