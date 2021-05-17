# 파이썬 데코레이터 (Decorator)

## 파이썬의 함수는 일급 시민이자 일급 객체

일급 객체(First-class object)란 다음과 같은 몇 가지 조건을 갖춤으로 인해서, 해당 객체를 사용할 때 다른 요소들과 아무런 차별이 없는 객체를 의미합니다. 다음은 [Robin Popplestone](https://en.wikipedia.org/wiki/Robin_Popplestone)이 정의한 일급 객체의 일반적인 조건입니다.

1. 모든 일급 객체는 함수의 실질적인 매개변수가 될 수 있다.
2. 모든 일급 객체는 함수의 반환값이 될 수 있다.
3. 모든 일급 객체는 할당의 대상이 될 수 있다. (변수 대입)
4. 모든 일급 객체는 비교 연산(==, equal)을 적용할 수 있다.

일급 객체는 자바스크립트에서 파생된 개념이지만 지금은 대다수 프로그래밍 언어에 적용되는 개념입니다. 파이썬에서는 모든 것이 객체이자 일급객체여서, 함수 역시 위 조건을 만족하는 일급 객체에 해당합니다.

​    

## 데코레이터란?

데코레이터란 기존 함수를 수정하지 않은 상태에서 새로운 기능을 추가할 때 사용하는 장식자입니다. 함수 위에 @를 붙인 것들이 모두 데코레이터에 해당됩니다.

```python
def basic_latte(func):
    def wrapper():
        print('Milk')
        func()
        print('Espresso')
    return wrapper


def vanilla():
    print('Vanilla Syrup')
    
def caramel():
    print('Caramel Syrup')
    

vanilla_latte = basic_latte(vanilla)
vanilla_latte()
print()
caramel_latte = basic_latte(caramel)
caramel_latte()

# 출력 결과
# Milk
# Vanilla Syrup
# Espresso
# 
# Milk
# Caramel Syrup
# Espresso
```

데코레이터의 이해를 위해 다양한 시럽을 베이스로 라떼를 제조해보는 예제로 데코레이터의 기본 구조를 살펴보겠습니다. 위의 basic_latte 함수는 우유와 에스프레소를 추가해주는 함수입니다. 특이한 점은 함수를 인자로 받고 내부에서 새로 정의한 함수 wrapper를 리턴하는 부분인데, 이렇게 하면 기존 함수 func을 매개변수로 사용해 추가 기능을 자유롭게 덧입힐 수 있습니다. 위와 같은 경우 vanilla 함수, caramel 함수에 각각 에스프레소와 우유를 덧입혀 출력한 것이죠! 파이썬의 closure의 개념을 알고 있다면, 이 예제 역시 closure의 일종으로 이해할 수 있습니다.

이 같은 구현이 가능한 이유는 파이썬의 함수가 일급 객체이기 때문입니다. 함수를 인자로 받고 리턴하고 변수에 할당하는 것이 가능함으로 인해 앞으로 강력하게 사용될 데코레이터가 탄생할 수 있었던 것이죠.

```python
def basic_latte(func):
    def wrapper():
        print('Milk')
        func()
        print('Espresso')
    return wrapper


@basic_latte
def vanilla():
    print('Vanilla Syrup')

@basic_latte
def caramel():
    print('Caramel Syrup')
    

vanilla()
print()
caramel()

# 출력 결과
# Milk
# Vanilla Syrup
# Espresso
# 
# Milk
# Caramel Syrup
# Espresso
```

데코레이터를 사용하면 위에서 살펴본 라떼 제조를 간단히 실행할 수 있습니다. 단순히 원하는 함수 위에 `@추가기능함수이름`을 달아주면, 굳이 basic_latte(vanilla)를 하지 않고 vanilla()만 실행해도 원하는 결과를 확인할 수 있습니다.

만일 여러개의 데코레이터를 지정하고 싶다면 다음과 같이 호출하면 됩니다.

```python
def espresso(func):
    def wrapper():
        func()
        print('Espresso')
    return wrapper


def milk(func):
    def wrapper():
        func()
        print('Milk')
    return wrapper


@espresso
@milk
def vanilla():
    print('Vanilla Syrup')
    
    
vanilla()

# 출력 결과
# Vanilla Syrup
# Milk
# Espresso
```

에스프레소와 우유를 각각 덧입혀 바닐라 라떼 제조에 성공했습니다! @를 쓰지 않았을 때의 코드 동작은 `espresso(milk(vanilla))()` 와 동일합니다.

​    

## 데코레이터에서 매개변수와 반환값을 처리하기

이번에는 매개변수와 반환값을 처리하는 데코레이터를 만들어 보겠습니다.

```python
def make_latte(func):
    def wrapper(espresso, milk):
        latte = func(espresso, milk)
        print(f'{func.__name__}(espresso={espresso}ml, milk={milk}ml) -> latte={latte}ml')
        return latte
    return wrapper

    
@make_latte
def mix(espresso, milk):
    return espresso + milk

print(mix(60, 200))

# 출력 결과
# mix(espresso=60ml, milk=200ml) -> latte=260ml
# 260
```

데코레이터가 매개변수를 처리할 수 있게끔 만드려면, 안쪽 wrapper 함수를 mix와 똑같은 형태로 매개변수를 받을 수 있게 만들어줘야 합니다. (결국 wrapper 함수가 인자를 받아 실행될 것이기 때문이죠!) 그리고 wrapper 함수 안에서 추가하고 싶은 기능을 만들어 줍니다. 여기서는 mix 함수를 실행한 리턴값을 변수로 저장하고 라떼 레시피와 제조 과정을 출력했습니다. 마지막으로 mix 함수는 에스프레소와 우유의 용량을 합친 수를 리턴해야 하므로, wrapper 함수에서 mix 함수의 반환값을 리턴해주도록 합니다. 만일 이를 잊어버리면, mix 함수를 호출해도 리턴값이 나오지 않으므로 유의해야 합니다.

이로써 매개변수와 반환값을 잘 처리하는 라떼 제조 데코레이터 구현에 성공했습니다. 만일 가변 인수 함수에 기능을 추가하고 싶은 상황이라면 데코레이터 안쪽 wrapper 함수에 *arg, **kwarg를 사용해주면 됩니다. 이렇게 만든 가변 인수 데코레이터는 고정 인수를 사용하는 일반적인 함수에도 사용할 수 있습니다.

​    

## 매개 변수가 있는 데코레이터 만들기

데코레이터의 또 하나 강력한 점은 인자를 받아 동적으로 적용되는 추가 기능을 덧입힐 수 있다는 것입니다. 

```python
def make_variation(syrup_name):  # 데코레이터의 인자를 추가하는 부분
    def make_latte(func):  # 실제 데코레이터 부분
        def wrapper(espresso, milk):
            latte = func(espresso, milk)
            print(f'{func.__name__}(espresso={espresso}ml, milk={milk}ml) with {syrup_name} syrup')
            print(f'-> {syrup_name}_latte={latte}ml')
            return latte
        return wrapper
    return make_latte  # 실제 데코레이터 함수 반환

    
@make_variation('green_tea')
def mix(espresso, milk):
    return espresso + milk

print(mix(60, 200))

# 출력 결과
# mix(espresso=60ml, milk=200ml) with green_tea syrup
# -> green_tea_latte=260ml
# 260
```

보통 기본 베이직 커피에 무언가를 더 가미해 다양한 맛을 낸 커피를 베리에이션(variation)이라고 하는데, 여기선 데코레이터의 인자로 시럽의 이름을 받아 기본 라떼의 베리에이션을 만들어 보겠습니다.

코드를 보면 기존에 만들었던 데코레이터와 큰 차이 없이, 단순히 데코레이터의 인자를 받을 함수를 하나 더 덧입혀 삼중으로 처리하고 wrapper 함수의 출력문을 조금 바꿨습니다. 그리고 mix 함수 위에는 새로 덧입힌 함수를 데코레이터로 사용하고 인자로 녹차시럽(green_tea)을 받았습니다. 이렇게 하면, 녹차시럽을 가미한 베리에이션으로 녹차 라떼가 완성됩니다. 데코레이터의 인자를 바닐라 시럽이나 카라멜 시럽으로 바꾸면 동적으로 다른 베리에이션을 만드는 것도 가능합니다.

> **여러 개의 데코레이터를 지정하다가 원래 함수의 이름이 나오지 않을 때**
>
> 여러 베리에이션을 만들면 다음과 같이 원래 함수의 이름이 나오지 않을 수 있습니다.
>
> ```python
> # 실제 동작: make_variation('green_tea')(make_variation('vanilla')(mix))(60, 200)
> @make_variation('green_tea')
> @make_variation('vanilla')
> def mix(espresso, milk):
>     return espresso + milk
> 
> print(mix(60, 200))
> # 결과 출력
> # mix(espresso=60ml, milk=200ml) with vanilla syrup
> # -> vanilla_latte=260ml
> # wrapper(espresso=60ml, milk=200ml) with green_tea syrup
> # -> green_tea_latte=260ml
> # 260
> ```
>
> 참고로 위 함수의 실제 동작은 `make_variation('green_tea')(make_variation('vanilla')(mix))(60, 200)`으로 실행됩니다. 이 때 원하지 않는 출력 결과로 wrapper 함수의 이름이 나타났는데, 이를 개선하려면 wrapper 함수 위에 functools 모듈의 wraps 데코레이터를 사용해야 합니다.
>
> ```python
> import functools
> 
> def make_variation(syrup_name):  
>     def make_latte(func):  
>         @functools.wraps(func)  # @functools.wraps에 func을 인자로 넣은 뒤 wrapper 함수 위에 지정
>         def wrapper(espresso, milk):
>             latte = func(espresso, milk)
>             print(f'{func.__name__}(espresso={espresso}ml, milk={milk}ml) with {syrup_name} syrup')
>             print(f'-> {syrup_name}_latte={latte}ml')
>             return latte
>         return wrapper
>     return make_latte
> 
>     
> @make_variation('green_tea')
> @make_variation('vanilla')
> def mix(espresso, milk):
>     return espresso + milk
> 
> print(mix(60, 200))
> # 결과 출력
> # mix(espresso=60ml, milk=200ml) with vanilla syrup
> # -> vanilla_latte=260ml
> # mix(espresso=60ml, milk=200ml) with green_tea syrup
> # -> green_tea_latte=260ml
> # 260
> ```
>
> @functools.wraps 데코레이터를 사용하면 출력 결과가 원하는대로 나오는 것을 확인할 수 있습니다. @functools.wraps 데코레이터는 원래 함수의 정보를 유지시켜 디버깅을 용이하게 합니다. 따라서 데코레이터를 만들 때 함께 사용하는 것이 유용합니다.

​    

## 클래스로 데코레이터 만들기

기존에 함수로 만들던 데코레이터는 클래스로도 만들 수 있습니다. 다만, 클래스로 데코레이터를 만들 때는 인스턴스를 함수처럼 호출하게 도와주는 \__call__ 매직 메서드를 사용해야 합니다.

```python
class basic_latte:
    def __init__(self, func):
        self.func = func
        
    def __call__(self):
        print('Milk')
        self.func()
        print('Espresso')

        
@basic_latte        
def vanilla():
    print('Vanilla Syrup')
    

vanilla()  # basic_latte(vanilla)() 형태로 동작해 인스턴스가 생성되고, ()로 인해 __call__ 메서드가 호출됨

# 출력 결과
# Milk
# Vanilla Syrup
# Espresso
```

이렇게 코드를 짜면 기존의 함수로 만든 데코레이터와 동일한 결과를 얻을 수 있습니다. 데코레이터로 인해 basic_latte(vanilla)가 먼저 동작해 basic_latte 클래스의 인스턴스가 생성되고 해당 인스턴스에 ()가 붙어 \__call__ 메서드가 수행되어 추가로 구현한 기능이 동작하게 됩니다.

클래스로 만든 데코레이터로 매개변수와 반환값도 처리할 수 있습니다.

```python
class make_latte:
    def __init__(self, func):
        self.func = func
        
    def __call__(self, *args, **kwargs):
        latte = self.func(*args, **kwargs)
        print('{}(espresso={}ml, milk={}ml) -> latte={}ml'.format(self.func.__name__, *args, latte))
        return latte

        
@make_latte        
def mix(espresso, milk):
    return espresso + milk

print(mix(60, 200))

# 출력 결과
# mix(espresso=60ml, milk=200ml) -> latte=260ml
# 260
```

\__call__ 메서드에 mix 함수가 받을 인자를 똑같이 받도록 만들고 mix 함수의 리턴 값을 \_\_call__메서드에서 반환해주면, 기존의 함수 데코레이터와 동일한 결과를 얻는 데코레이터를 클래스로 만들 수 있습니다.

매개 변수가 있는 데코레이터도 클래스로 구현해보겠습니다.

```python
class make_variation:
    def __init__(self, syrup_name):
        self.syrup_name = syrup_name
        
    def __call__(self, func):
        def wrapper(*args, **kwargs):
            latte = func(*args, **kwargs)
            print('{}(espresso={}ml, milk={}ml) with {} syrup'.format(func.__name__
                                                                      , *args, self.syrup_name))
            print(f'-> {self.syrup_name}_latte={latte}ml')
            return latte
        return wrapper

        
@make_variation('green_tea')        
def mix(espresso, milk):
    return espresso + milk

print(mix(60, 200))

# 출력 결과
# mix(espresso=60ml, milk=200ml) with green_tea syrup
# -> green_tea_latte=260ml
# 260
```

\__init__ 메서드에서 데코레이터의 인자를 초깃값으로 받으면서, 인스턴스 속성으로 저장합니다. 그리고 \_\_call__ 메서드에서 함수를 인자로 받도록 하고, 메서드 내부에 wrapper 함수를 새로 만들어 호출할 함수와 똑같은 형태로 매개변수를 받을 수 있도록 만들어 줍니다. 추가할 기능 역시 wrapper 함수에 구현하고 \_\_call__ 메서드가 wrapper 함수를 리턴하도록 합니다. 그리고 mix 함수의 반환 값을 wrapper 함수가 리턴하도록 만들면 인자를 받는 데코레이터 구현이 완료됩니다. 똑같이 녹차 라떼가 제조됨을 확인할 수 있죠!

​    

## 데코레이터의 의의

이로써 파이썬에서 데코레이터를 만드는 다양한 형태와 방법을 살펴봤습니다. 클로저 개념에서 발전되어 등장한 데코레이터는 기존 함수를 변형하지 않고 새로운 기능을 추가하는 목적으로 사용하지만, 디버깅에서도 훌륭한 수단이 됩니다. 함수의 성능 측정이나 함수 실행 전 데이터 확인 같은 다양한 목적으로도 사용되므로, 데코레이터에 익숙해지는 것은 효과적인 프로그래밍에 큰 도움이 될 것입니다.

​    

## Reference

[python의 함수 decorators 가이드](https://medium.com/sjk5766/%EB%B2%88%EC%97%AD-python%EC%9D%98-%ED%95%A8%EC%88%98-decorators-%EA%B0%80%EC%9D%B4%EB%93%9C-2cd9d5151a1d)

[파이썬 코딩 도장 -  데코레이터](https://dojang.io/mod/page/view.php?id=2427)

[1급 객체(first-class object)란?](https://jcsoohwancho.github.io/2019-10-18-1%EA%B8%89-%EA%B0%9D%EC%B2%B4(first-class-object)%EC%9D%B4%EB%9E%80/)

