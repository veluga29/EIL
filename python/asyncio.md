# 비동기 프로그래밍을 돕는 asyncio 라이브러리

## asyncio(Asynchronous I/O)

파이썬은 인터프리터 언어의 특성과 함께 속도가 느린 언어로 알려져있습니다. 그렇기에 비동기로 처리해 속도를 높이는 방법은 파이썬의 단점을 극복하는 하나의 해답이 됩니다. 다만, 파이썬에는 멀티스레드에서 발생하는 복잡한 문제들을 막기 위한 GIL(Global Interpreter Lock)이 존재하고, 이로 인해 항상 한 번에 하나의 스레드만 작업을 수행할 수 있어 진정한 의미의 멀티스레딩은 실현되기 어렵습니다. GIL은 보다 복잡한 문제를 막기 위한 심플하고 효과적인 방법이지만, 파이썬의 한계점이자 파이썬이 태생적으로는 비동기 프로그래밍에 적합하지 않은 언어임을 보여주죠.

한계가 뚜렷함에도 파일 읽기 및 쓰기, Http 통신 대기와 같은 Blocking I/O 상황에서는 비동기 프로그래밍이 여전히 파이썬에서 위력을 발휘합니다. 이러한 상황의 비동기 프로그래밍을 좀 더 간편하게 하기 위해 나온 모듈이 asyncio입니다. 그리고 asyncio로 인한 변화 덕분에, 파이썬에서도 점점 비동기 프로그래밍 사용이 용이해지고 있습니다.

asyncio는 비동기 프로그래밍을 위한 모듈로, async/await 구문을 사용해 CPU 작업과 I/O 작업을 병렬로 처리할 수 있도록 도와줍니다. asyncio 모듈은 파이썬 3.4에 새로이 추가되었고, 3.5 부터 `async def`와 `await` 구문이 지원되었습니다. 그래서 파이썬 3.4 미만에서는 비동기 프로그래밍을 `@asyncio.coroutine` 데코레이터와 `yield from`을 사용해 구현해야 합니다. 3.3의 경우 `pip install asyncio`로 모듈을 설치하고 데코레이터와 `yield from`을 사용하면 됩니다.

​    

> **동기(synchronous) 처리**
>
> 특정 작업이 끝나면 다음 작업을 처리하는 순차처리 방식입니다. (프로그램의 코드가 순차적으로 처리되는 방식의 프로그래밍을 말합니다.) 아래 코드처럼 main 함수의 코드들이 작성된 순서대로 처리되는 경우 동기적으로 처리되었다고 말합니다. 특정 작업을 멈출 때도 비동기 프로그래밍에서 사용하는 `asyncio.sleep`과 대비되게 time 모듈을 사용합니다.
>
> ```python
> import time
> 
> def main():
>     print('time')
>     foo('text')
>     print('finished')
> 
> 
> def foo(text):
>     print(text)
>     time.sleep(2)
>     
> main()
> 
> # 실행 결과
> # time
> # text
> # finished
> ```

> **비동기(asynchronous) 처리**
>
> 여러 작업을 처리하도록 예약한 뒤 작업이 끝나면 결과를 받는 방식입니다. (프로그램의 코드가 여러 프로세스 여러 스레드로 나뉘어 처리되는 방식을 말합니다.) 코드 아래에서 이어 설명하겠습니다.

​    

## 비동기 함수, 네이티브 코루틴

코루틴은 필요에 따라 일시정지할 수 있는 함수를 말합니다. 코루틴은 다양한 언어에 존재하고 여러 형태로 구현될 수 있습니다. 특히, 파이썬에서는 제너레이터에 기반한 코루틴과 구분하기 위해, `async def`로 만든 코루틴을 네이티브 코루틴이라고 부릅니다. 이러한 코루틴 함수를 비동기 함수라고도 부르며, 네이티브 코루틴은 앞서 이야기한 것처럼 파이썬 3.5에서부터 등장합니다.

```python
import asyncio
 
    
async def main():    # async def로 네이티브 코루틴을 만듦
    print('Hello, world!')

    
asyncio.run(main())  # main 코루틴 함수를 실행

# 실행 결과
# Hello, world!
```

간단한 네이티브 코루틴을 구현했습니다. 먼저 `async def`로 네이티브 코루틴을 만듭니다. 그리고 `async def` 함수 범위 바깥에서 코루틴 함수를 실행하기 위해, `asyncio.run(코루틴 객체)`을 사용합니다. 네이티브 코루틴 함수를 호출하면 코루틴 객체를 생성하므로, 이를 `asyncio.run()`에 넣어주면 됩니다. 이렇게 하면 비동기 함수의 실행이 완료되고 생각했던 출력 결과를 얻습니다. 하지만 아직 코드에는 비동기적인 느낌이 없습니다. 

​    

## await으로 네이티브 코루틴 실행하기

이번엔 조금 더 비동기적인 느낌을 내어 프로그램을 짜보겠습니다. 이를 위해, `await`이 필요합니다.

`await`은 네이티브 코루틴 함수 내에서만 사용할 수 있으며, 두 가지 기능을 수행합니다. 첫 번째 기능은 **코루틴 함수를 실행(execute)**하는 것입니다. 원래 `async def` 구문에서는 `await`이 코루틴 함수를 실행하는 키워드입니다. 하지만 앞서 말했듯 `await`은 `async def` 함수 내부에서만 사용이 가능하기 때문에, **코루틴 함수 밖에서 코루틴 함수를 실행할 때는 앞에서 봤던 `asyncio.run()`을 사용합니다.** 두 번째 기능은 **`await` 키워드 의미 그대로 await에 지정된 코루틴 함수가 종료될 때까지 기다리는 것**입니다. 실제로 await 뒤에 코루틴 객체, 퓨처 객체, 태스크 객체를 지정할 수 있으며, 해당 객체가 끝날 때까지 기다린 뒤 결과를 반환합니다. (3가지 객체는 코루틴과 관련된 객체들이며 보통 어웨이터블(awaitable) 객체로 불립니다. 여기서는 코루틴 함수를 호출하면 리턴되는 코루틴 객체와 이후 만들 태스크 객체만 다루겠습니다.) 용법은 다음과 같고 변수에 할당하지 않아도 되지만, 할당한다면 해당 코루틴 함수가 `return`하는 값이 담깁니다.

- `변수 = await 코루틴객체`
- `변수 = await 퓨처객체`
- `변수 = await 태스크객체`

await을 사용해 네이티브 코루틴을 실행해보겠습니다.

```python
import asyncio


async def main():
    print('time')
    await foo('test')
    print('finished')
    
async def foo(text):
    await asyncio.sleep(1)
    print(text)
    
asyncio.run(main()) 

# 출력 결과
# time
# test
# finished
```

이 경우 'time'이 출력되고 1초 후 ' test'와 'finished'가 출력되면서 네이티브 코루틴이 잘 실행됨을 확인할 수 있습니다.

다만, 실제 비동기 프로그래밍이라면 프로그램의 코드가 여러 스레드로 동시에 작업을 수행하기 때문에, `foo` 함수에서 1초를 기다리는 동작은 수행하더라도, 실제로 1초도 되기전에 'time', 'test', 'finished'가 모두 출력되며 프로그램이 마무리될 것입니다. (`foo` 함수에서 1초 기다리는 코드 `asyncio.sleep(1)`이 완료되기 전에 `main` 함수가 먼저 종료되기 때문에 `foo` 함수 종료 이전에 프로그램 자체가 먼저 종료될 것입니다!) 그래서 실제 비동기 실행을 위해서는 `task` 객체를 사용해야 합니다.

​    

## Task 객체를 생성해 비동기 실행하기

Task를 사용하면 실제로 비동기 실행을 할 수 있습니다.

```python
import asyncio


async def main():
    print('time')
    asyncio.create_task(foo('test'))
    print('finished')
    
async def foo(text):
    asyncio.create_task(asyncio.sleep(1))
    print(text)
    
asyncio.run(main()) 

# 출력 결과
# time
# finished
# test
```

`await`은 코루틴을 실행하는 역할과 해당 코루틴 함수가 종료될 때까지 기다리는 역할을 수행합니다. 그러나 `await`만으로는 여러 스레드에서 동시에 프로그램이 실행되는 비동기적인 실행이 되지 않습니다. 이러한 비동기적 실행을 위해서 `task` 객체를 사용합니다. `asyncio.create_task(코루틴 객체)`를 사용하면 해당 코루틴 객체에 대한 `task` 객체를 생성함과 동시에 해당 코루틴 함수를 비동기적으로 실행합니다. 즉, `await`을 사용하지 않아도 실제 비동기적으로 코루틴 함수를 실행합니다. 따라서, 위 코드는 앞서 이야기한 것처럼 'time'을 출력한 후 1초를 기다리지 않고 'finished'와 'test'가 출력됩니다.

'finished'가 'test'보다 먼저 출력된 이유는 context switch에 의한 것으로 예상됩니다. 정확한 알고리즘은 알 수 없지만, 스레드가 서로 교차하다가 'finished' 출력 스레드가 먼저 완료되고 그 다음 'test' 출력 스레드가 완료되며 함수가 종료되었을 것입니다.

또한, `asyncio.create_task(asyncio.sleep(1))`에 해당하는 스레드는 아직 수행 중이겠지만, 1초가 지나기 이전에 `main` 함수가 종료되면서 프로그램은 종료됩니다. 만일 `asyncio.create_task(asyncio.sleep(1))` 코드의 수행을 온전히 완료하고 프로그램을 종료하고 싶다면, 다음과 같이 원하는 위치에서 `await`으로 함수 종료를 기다리면 됩니다.

```python
import asyncio


async def main():
    print('time')
    await asyncio.create_task(foo('test'))
    print('finished')
    
async def foo(text):
    await asyncio.create_task(asyncio.sleep(1))
    print(text)
    
asyncio.run(main()) 

# 출력 결과
# time
# test
# finished
```

이렇게 되면, 프로그램은 비동기적으로 실행했지만 원하는 위치에서 임의로 함수의 종료를 기다린 후 다음 동작을 실행하게끔 할 수 있습니다. 위 경우 비동기적으로 함수를 실행했음에도 'time' 출력 후 1초 기다린 다음 'test'와 'finished'가 출력됨을 확인할 수 있습니다.

​    

## 조금 더 심화된 비동기 예제 살펴보기

앞의 내용들을 적용하여 조금 더 심화된 비동기 코드를 살펴보겠습니다.

```python
import asyncio


async def main():
    print('time')
    asyncio.create_task(foo('test'))
    await asyncio.sleep(0.5)
    print('finished')
    await asyncio.sleep(1)
    
async def foo(text):
    await asyncio.sleep(1)
    print(text)
    await asyncio.sleep(1)
    print(text)
    
asyncio.run(main()) 

# 출력 결과
# time
# finished
# test
```

위 코드는 task 객체를 생성해 비동기적으로 `foo` 함수를 실행합니다. 코드 수행이 여러 스레드로 나뉘어 동시에 이뤄지므로, 위 코드의 경우 `main` 함수와 `foo` 함수가 동시에 병렬적으로 실행되는 상황입니다. 시간 단위로 살펴보면 다음과 같습니다.

* 약 0초: 'time'이 출력되면서 `main`과 `foo` 함수가 분기됩니다.
* 약 0.5초:  `main` 함수의 `await asyncio.sleep(0.5)` 코루틴이 종료되고 'finished'가 출력됩니다.
* 약 1초: `foo` 함수의 `await asyncio.sleep(1)` 코루틴이 종료되고 'test'가 출력됩니다.
* 약 1.5초: `main` 함수의 `await asyncio.sleep(1)` 코루틴이 종료되면서 `main` 함수 종료와 함께 프로그램이 종료됩니다.

따라서 `foo` 함수의 마지막 'test'는 출력되지 않습니다. 

​    

## Reference

[Python3.8 asyncio, async/await 기초 - 코루틴과 태스크](https://kdw9502.tistory.com/6)

[asyncio 사용하기](https://dojang.io/mod/page/view.php?id=2469)

[Python 3, asyncio와 놀아보기](https://tech.ssut.me/python-3-play-with-asyncio/)

