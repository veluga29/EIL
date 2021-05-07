### 2진수, 8진수, 16진수로 정수 표현하기

```python
>>> 0b110  # 2진수
6
>>> 0o10  # 8진수
8
>>> 0xF  # 16진수
15
```

​    

### 보다 정교한 계산으로 부동소수점 오류를 피하는 자료형 Decimal

```python
from decimal import Decimal


cost_of_gum = Deciaml('0.10')
cost_of_gumdrop = Decimal('0.35')

cost_of_transaction = cost_of_gum + cost_of_gumdrop
print(cost_of_transaction)
# Returns 0.45 instead of 0.44999999999999996
```

​    

### 빈 변수 만들기

```python
>>> x = None  # 다른 언어의 null 값
>>> print(x)
None
```

​     

### del 키워드가 사용되는 경우

* 변수 삭제, 리스트 요소 삭제, 딕셔너리 요소 삭제

![img](https://blog.kakaocdn.net/dn/cq2mye/btq2KxieKbM/bzLz3SpTWygTnkpAQERw6k/img.png)

​    

### 언더스코어 변수( _ )

* 파이썬 셸에서 코드를 실행했을 때 결과는 _(밑줄 문자) 변수에 저장됩니다. 따라서 _를 사용하면 직전에 실행된 결과를 다시 가져올 수 있습니다.

​    

### 단락 평가(short-circuit evalution)

* 단락 평가란 **첫 번째 값만으로 결과가 확실할 때 두 번째 값은 확인(평가)하지 않는 방법**을 말합니다. 논리 연산에서 단락 평가는 중요합니다. 예를 들어, Fasle and True는 and 앞이 False이기 때문에 뒷 부분을 고려할 필요없이 결과가 False가 됩니다. 실제 연산에서도 False and True는 단락 평가를 진행해 앞 부분만 확인하여 결과를 리턴합니다. 따라서, 복잡한 논리 연산일수록, 전체 결과를 빠르게 판단할 수 있는 식이 있다면 최대한 앞으로 빼서 효율적으로 연산이 동작하게끔 작성해야 합니다.

* 또한, 파이썬에서 **논리 연산자는 마지막으로 단락 평가를 실시한 값을 그대로 반환하는 점**을 유의해야 합니다. 논리 연산자는 무조건 불을 반환하지 않습니다.

```python
True and 'Welsh Corgi'  # 'Welsh Corgi' 리턴
'Welsh Corgi' and True  # True 리턴
'Welsh Corgi' and False  # False 리턴
False and 'Welsh Corgi'  # False 리턴
0 and 'Welsh Corgi'  # 0 리턴
```

​    

### 자료형(객체) 구분

#### 1. 시퀀스 자료형

* 리스트, 튜플, range, 문자열처럼 값이 연속적으로 이어진 자료형을 시퀀스 자료형(sequence types)라고 부릅니다.

![img](https://blog.kakaocdn.net/dn/c4nCM2/btq2IiF07g8/LZqqy4yx7xfQaLuxbKN5bK/img.png)

> 수행 가능 연산:

* in 연산자

* +, * 연산 (range 제외)

* len() 함수

* 인덱싱([] ∝ __getitem__ 메서드) & 슬라이싱(∝ 슬라이스 객체 생성 후 [] 또는 __getitem__ 메서드에 삽입)

* 인덱스로 값 할당 및 del 삭제 (list만 가능, 다만 범위를 벗어나면 안됨)

* 슬라이싱으로 값 할당 및 del 삭제 (list만 가능)

​    

> 슬라이싱으로 값 할당 및 del 삭제

* 슬라이싱으로 범위를 지정해 값 할당 및 삭제를 진행할 때, 새 리스트를 생성하지 않고 기존 리스트를 변경합니다.

* 범위와 요소의 개수가 정확히 일치하지 않아도 됩니다.
  * 예 1)

    ``````python
    a = [0, 10, 20, 30, 40, 50, 60, 70, 80, 90]
    a[2:5] = ['a']    # 인덱스 2부터 4까지에 값 1개를 할당하여 요소의 개수가 줄어듦
    a
    [0, 10, 'a', 50, 60, 70, 80, 90]
    ``````

  * 예 2)

    ``````python
    a = [0, 10, 20, 30, 40, 50, 60, 70, 80, 90]
    a[2:5] = ['a', 'b', 'c', 'd', 'e'] # 인덱스 2부터 4까지 값 5개를 할당하여 요소의 개수가 늘어남
    a
    [0, 10, 'a', 'b', 'c', 'd', 'e', 50, 60, 70, 80, 90]
    ``````

  * 예 3)

    ``````python
    a = [0, 10, 20, 30, 40, 50, 60, 70, 80, 90]
    a[2:5] = ['a', 'b', 'c']    # 인덱스 2부터 4까지 값 할당
    a
    [0, 10, 'a', 'b', 'c', 50, 60, 70, 80, 90]
    ``````

* 범위에 인덱스 증가폭을 설정해서 값을 할당할 수도 있습니다. (다만, 이 때는 범위에 해당하는 요소 개수와 할당할 요소의 개수가 일치해야 합니다.)

  * 예)

    ```python
    a = [0, 10, 20, 30, 40, 50, 60, 70, 80, 90]
    a[2:8:2] = ['a', 'b', 'c']    # 인덱스 2부터 2씩 증가시키면서 인덱스 7까지 값 할당
    a
    [0, 10, 'a', 30, 'b', 50, 'c', 70, 80, 90]
    ```

* del을 사용해 일반적으로 값을 삭제할 수 있지만, 인덱스 증가폭을 사용해서 값을 삭제할 수도 있습니다.

  * 예)

    ```python
    a = [0, 10, 20, 30, 40, 50, 60, 70, 80, 90]
    del a[2:8:2]    # 인덱스 2부터 2씩 증가시키면서 인덱스 6까지 삭제
    a
    [0, 10, 30, 50, 70, 80, 90]
    ```

​    

#### 2. 반복 가능한(iterable) 객체

* 문자열, 리스트, 딕셔너리, 세트 같이, 요소가 여러 개 들어있고, 한 번에 하나씩 꺼낼 수 있는 객체입니다. 반복 가능한 객체는 __iter__ 메서드를 포함하고 있으며, 이 메서드를 호출해 이터레이터(iterator)를 생성할 수 있습니다.

![img](https://blog.kakaocdn.net/dn/chskdx/btq2IPXVnEB/5RzMO7dKq4gH93HLg2Kx61/img.png)

​    

#### 3. 변경 가능한(Mutable) 객체 

![img](https://blog.kakaocdn.net/dn/bwYqS4/btq2NGdC7d8/mu7tDrRv5jc0TFWGjZkVo0/img.jpg)

* 객체의 값이나 요소가 변경 가능한지 아닌지에 따라 나뉘는 기준이다. Mutable한 객체 list, dict, set을 외워두는게 기억하기 편리하다.

​    

> 참고하기 좋은 표

![img](https://blog.kakaocdn.net/dn/eIYPX7/btq2Mod0okJ/mTf7JwW81whk02Jko46Gw1/img.png)

​    

### 얕은 복사와 깊은 복사

* 얕은 복사 (=주소값 복사)
  * 파이썬은 모든 변수에 주소값이 담기므로, 단순히 변수에 할당하는 방식으로는 새로운 객체를 복사하는 것이 아니라 동일한 객체를 가리키게 된다.

```
a = [0, 0, 0, 0, 0]
b = a
a is b
True
```

* 깊은 복사 (1차원)
  * 파이썬 내장함수 copy()
  * 슬라이싱을 통한 복사 ex) a[:]

* 깊은 복사 (다차원)
  * copy 모듈의 deepcopy() 메서드 사용

​    

### 튜플을 사용하는 이유

리스트가 언제든 요소를 추가할 수 있게 하기 위해 실제 데이터보다 큰 메모리를 사용하는데 반해, 튜플은 요소 변경이 없어 고정된 메모리를 사용합니다. 또한, 튜플의 구조는 간단해서 리스트보다 빠른 성능을 보여줍니다. 따라서, 요소 변경이 없는 상황에서는 튜플을 사용하는 것이 메모리를 아끼고 성능을 높이는 방법입니다.

​    

### defaultdict를 사용해 기본값이 빈 리스트인 딕셔너리 생성하기

```python
from collections import defaultdict

a = defaultdict(list)
a['x']
a['y']
print(a)
defaultdict(<class 'list'>, {'x': [], 'y': []})
```

​    

### 딕셔너리에서 요소를 삭제하는 방법

* 딕셔너리에서 요소를 삭제하는 방법은 제한적이기 때문에 보통 다음과 같은 방법을 사용한다.

#### 1. 키를 통해 삭제하기

* del 예약어, pop('키', 기본값) 메서드 사용합니다. popitem() 메서드를 사용하면, 파이썬 3.6 이상에서는 딕셔너리의 가장 마지막에 있는 키, 값 쌍을 삭제하여 튜플로 반환하고, 3.6 미만에서는 임의의 키, 값 쌍을 삭제하여 튜플로 반환합니다.

#### 2. 값을 통해 삭제하기 (특정 값을 제외하여 새로 딕셔너리를 생성)

* 딕셔너리 표현식을 사용합니다.

```python
x = {'a': 10, 'b': 20, 'c': 30, 'd': 40}
x = {key: value for key, value in x.items() if value != 20}
x
{'a': 10, 'c': 30, 'd': 40}
```

​    

### 파일 객체는 이터레이터입니다!

* open을 통해서 가져오는 파일 객체는 이터레이기 때문에, for문에서 반복하거나 언패킹할 수 있습니다.

```python
file = open('welsh.txt', 'r')
a, b, c = file
a, b, c
('안녕하세요.\n', '멍멍!\n', '저는 웰시 코기입니다.\n')
```

​    

### random 모듈에서 자주 사용되는 메서드들

```python
import random

# 0이상 1미만 범위의 난수 생성
random.random()  # return: 0 <= x < 1에 해당하는 x 값

# 지정한 범위에 해당하는 정수 하나를 랜덤하게 가져오기
random.randint(1, 16)  # return: 1 <= x <= 16에 해당하는 int 타입 x 값

# 지정한 범위에 해당하는 실수 하나를 랜덤하게 가져오기
random.uniform(1, 20)  # return: 1.0 <= x < 20.0에 해당하는 float 타입 x 값

# range(start, stop, step) 함수로 만들어지는 정수들 중 랜덤하게 하나를 가져오기
random.randrange(1, 9, 2)  # return: 1, 3, 5, 7 중 하나의 값


seq = ['a', 'b', 'c', 'd']

# 시퀀스 객체 내 요소 순서를 무작위로 변경하기
random.shuffle(seq)  # seq: ['c', 'b', 'a', 'd']

# 시퀀스 객체에서 요소 하나를 랜덤하게 가져오기
random.choice(seq)  # return: 'c', 'b', 'a', 'd' 중 하나의 값

# 시퀀스 객체에서 요소 여러 개를 랜덤하게 가져오기
random.sample(seq, 2)  # return: seq 리스트에서 2개의 요소를 뽑아 리스트로 만들어 리턴
```



### datetime 모듈 사용법

```python
from datetime import datetime

# datetime 객체 생성하기

# datetime(년, 월, 일, 시간, 분, 초)
birthday = datetime(1994, 6, 27)  # datetime.datetime(1994, 6, 27, 0, 0)
birthday = datetime(1994, 6, 27, 6, 30, 27)  # datetime.datetime(1994, 6, 27, 6, 30, 27)
# year, month, day, hour, minute, second 속성에 접근 가능
birthday.year  # 1994
birthday.month  # 6
# weekday() 메서드를 사용하면 요일을 0(월) ~ 6(일) 인덱스로 반환
birthday.weekday()  # 0


# 현재 시간으로 datatime 객체 생성하기
datetime.now()  # datetime.datetime(2021, 5, 7, 23, 46, 7, 925228)


# datetime 객체로 두 날짜 사이의 시간 차이 구하기
datetime(2021, 1, 2) - datetime(2020, 1, 1)  # datetime.timedelta(days=367)
datetime.now() - datetime(2021, 1, 1)  # datetime.timedelta(days=126, seconds=86052, microseconds=468421)


# 문자열로 된 시간을 datetime 객체로 파싱하기
parsed_date = datetime.strptime('Jan 15, 2019', '%b %d, %Y')
parsed_date.month  # 1
parsed_date.day  # 15
parsed_date.minute  # 0


# datetime 객체를 문자열로 만들기
date_string = datetime.strftime(datetime.now(), '%b %d, %Y')
date_string  # 'May 08, 2021'
```

> strftime() and strptime() Format Codes 는 다음 링크에서 확인
> https://docs.python.org/3/library/datetime.html

​    

### Reference

[파이썬 코딩 도장](dojang.io/course/view.php?id=7)

[파이썬 del - 제타위키](zetawiki.com/wiki/%ED%8C%8C%EC%9D%B4%EC%8D%AC_del)

[파이썬 기초](https://hvyair.tistory.com/15)

[Python, 파이썬 - Call by assignment, mutable, immutable, 파이썬 복사(Python Copy)](engkimbs.tistory.com/667)