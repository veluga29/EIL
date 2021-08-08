## Async-Await

`async`, `await`을 사용하는 구문은 ES8에서 소개된 JavaScript의 비동기 처리를 위한 syntactic sugar입니다. 비동기 처리하는 과정이나 결과는 이전 callback 함수를 통해 구현하는 방식이나 혹은 ES6에서부터 사용하는 promise 객체를 사용해 구현하는 방식과 동일하지만, 문법적으로 조금 더 편리하게 비동기 처리를 할 수 있도록 제공됩니다.



## `async` keyword

```javascript
async function myFunc() {
  // Function body here
};
 
myFunc();
```

비동기 함수를 만들기 위해 사용하는 키워드입니다. 구현한 비동기 처리 로직은 위와 같이 `async`로 선언된 함수로 감싸서 의도대로 실행할 수 있습니다.

```javascript
const myFunc = async () => {
  // Function body here
};
 
myFunc();
```

또한, `async` 함수는 함수 선언식 뿐만 아니라 함수 표현식으로도 사용할 수 있습니다.

​    

## `async` 함수의 리턴 값

`async` 함수는 항상 promise 객체를 리턴합니다. 덕분에, 원래의 promise 비동기 처리 방식대로 `.then()`, `.catch()` 등을 그대로 사용할 수 있습니다. 다만, 리턴할 때 3가지 상황에 따라 다른 promise 객체를 내어줍니다.

* 명시적으로 리턴하는 값이 없을 때: `undefined`를 resolved value로 사용하는 promise 객체를 리턴합니다.
* 명시적으로 promise 객체가 아닌 값을 리턴할 때: 해당 리턴 값을 resolved value로 사용하는 promise 객체를 리턴합니다.
* 명시적으로 promise 객체를 리턴할 때: 해당 promise 객체를 그대로 리턴합니다.

​    

## `await` keyword

`async` 키워드 만으로는 비동기 처리를 제대로 할 수 없기 때문에, `async` 함수 안에서는 보통 `await`을 함께 사용합니다.

`await`은 지정한 함수에서 promise 객체가 리턴 및 resolve될 때까지 `async` 함수 실행 자체를 멈추었다가, promise의 resolved value를 받으면 해당 값을 리턴하고 `async` 함수의 남은 코드를 다시 실행하는 키워드입니다. 즉, promise를 객체를 받아 해당 promise 객체를 pending 상태에서 resolved 상태까지 실행하여 resolved value를 리턴하는 전 과정을 포괄합니다. 이러한 특이성으로 인해, `await`은 주로 라이브러리에서 가져온 promise를 리턴하는 함수와 함께 사용하는 것이 일반적입니다.

```javascript
async function asyncFuncExample(){
  let resolvedValue = await myPromise();
  console.log(resolvedValue);
}
 
asyncFuncExample(); // Prints: I am resolved now!
```

위 코드에서 `myPromise()`는 `"I am resolved now!"`라는 string을 resolve할 promise를 리턴하는 함수입니다. 이렇게 promise의 로직을 인지하며 `await`을 사용하면, 비동기적인 코드가 순차적인 코드 흐름으로 읽히도록 구현할 수 있습니다.

​    

## Error handling with `try... catch`

```javascript
async function usingTryCatch() {
 try {
   let resolveValue = await asyncFunction('thing that will fail');
   let secondValue = await secondAsyncFunction(resolveValue);
 } catch (err) {
   // Catches any errors in the try block
   console.log(err);
 }
}
 
usingTryCatch();
```

기존의 promise 객체 비동기 처리 방식에서 chain이 길어질 때, `.catch`를 사용해도 어떤 순서에서 error가 발생한 것인지 파악하기 어려웠습니다. 반면에, `async... await`에서는 `try... catch`를 사용해 쉽게 error handling을 진행할 수 있습니다.

`async` 함수에서 `try... catch`는 동기적인 코드와 같은 방식으로 error handling을 할 수 있으면서 동시에, 동기 및 비동기 error 모두를 잡아낼 수 있기 때문에, 쉬운 디버깅을 가능하게 한다는 큰 이점이 있습니다. 

```javascript
async function usingPromiseCatch() {
   let resolveValue = await asyncFunction('thing that will fail');
}
 
let rejectedPromise = usingPromiseCatch();
rejectedPromise.catch((rejectValue) => {
console.log(rejectValue);
})
```

물론 `async` 함수도 promise 객체의 `.catch` 메서드를 종종 사용할 때가 있습니다. 위와 같이, 복잡한 코드의 마지막 에러만 잡아내고 싶을 경우 global scope에서 사용하는 것이 하나의 예입니다.

​    

## 독립적인 promise들을 다루는 방법

다수의 promise 객체들이 서로 의존하고 있을 때는 promise마다 `await`을 사용하여 명확한 순서로 비동기 처리를 하는 것이 효율적입니다. 반면에, promise 객체들이 서로 독립적일 때는 순서에 상관없이 모든 promise가 동시에 실행되는 것이 보다 효율적입니다.

`async` 함수에서 앞서 이야기한 concurrent 실행을 진행하는 방법을 크게 2가지 소개하겠습니다.

* await in one line

  ```javascript
  /* 원래의 모습
  async function waiting() {
   const firstValue = await firstAsyncThing();
   const secondValue = await secondAsyncThing();
   console.log(firstValue, secondValue);
  }
  */ 
  
  // concurrent 실행
  async function concurrent() {
   const firstPromise = firstAsyncThing();
   const secondPromise = secondAsyncThing();
  console.log(await firstPromise, await secondPromise);
  }
  ```

* use `Promise.all`

  ```javascript
  async function asyncPromAll() {
    const resultArray = await Promise.all([asyncTask1(), asyncTask2(), asyncTask3(), asyncTask4()]);
    for (let i = 0; i<resultArray.length; i++){
      console.log(resultArray[i]); 
    }
  }
  ```

​    

## Reference

[Codecademy - introduction to javascript](https://www.codecademy.com/courses/introduction-to-javascript/)