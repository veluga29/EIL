## Parameter type annotation

변수에 type annotation을 했던 것처럼, 타입스크립트는 함수의 파라미터에 type annotation을 하여 파라미터가 원하는 데이터 타입을 가지도록 할 수 있습니다.

물론, 기존 자바스크립트에서도 파라미터의 타입을 validation할 수 있는 방법이 있습니다.

```javascript
function printLengthOfText(text) {
  if (typeof text !== 'string') {
    throw new Error('Argument is not a string!');
  }
 
  console.log(text.length);
}
 
printLengthOfText(3); // Error: Argument is not a string!
```

다만 조건문을 만들고 error를 일으키는 작업이 조금 번거롭습니다.

타입스크립트는 이러한 불편함을 type annotation을 사용해 다음과 같이 간단히 해결합니다.

```typescript
function printKeyValue(key: string, value) {
  console.log(`${key}: ${value}`);
}
 
printKeyValue('Courage', 1337); // Prints: Courage: 1337
printKeyValue('Mood', 'scared'); // Prints: Mood: scared
```

이로 인해, `key`는 string 타입을 가져야 하며, annotation이 없는 value는 `any` 타입을 부여받게 됩니다.

​    

## Optional parameter

```typescript
function greet(name: string) {
  console.log(`Hello, ${name || 'Anonymous'}!`);
}
 
greet('Anders'); // Prints: Hello, Anders!
greet(); // TypeScript Error: Expected 1 arguments, but got 0.
```

JavaScript는 인자 없이 `greet()`을 실행했을 때, `name`은 `undefined`가 되고 이는 falsy value로 인식되어 결국 'Hello, Anonymous`가 콘솔에 출력될 것입니다. 그러나 타입스크립트는 optional을 따로 지정해주지 않으면 이에 대하여 오류를 일으킵니다.

따라서 optional 파라미터를 사용하고 싶다면, 다음과 같이 파라미터 뒤에 `?`를 사용해 해당 파라미터가 optional 함을 선언해줍니다.

```typescript
function greet(name?: string) {
  console.log(`Hello, ${name|| 'Anonymous'}!`);
}
 
greet(); // Prints: Hello, Anonymous!
```

​    

## Default parameter

파리미터의 기본값을 지정해주면 해당 파리미터는 optional해지며 동시에 기본값의 타입과 동일한 타입의 데이터가 인자로 올 것이 전제됩니다.

```typescript
function greet(name = 'Anonymous') {
  console.log(`Hello, ${name}!`);
}
```

위 코드에 대해 인자없이 `greet()`을 실행하면, 'Hello, Anonymous!'를 출력합니다. 반면에, `greet(3)`과 같이 number 값을 인자로 전달하면 타입 에러를 야기합니다. 이는 `name`의 인자로 string 혹은 undefined 값이 올 것이라고 파라미터의 default 값으로 인해 설정되었기 때문입니다.

​    

## Inferring return type

타입스크립트는 함수의 리턴 값의 타입 역시 추론합니다.

```typescript
function ouncesToCups(ounces: number) {
  return `${ounces / 16} cups`;
}
 
const liquidAmount: number = ouncesToCups(3);
// Type 'string' is not assignable to type 'number'.
```

예를 들어, `ouncesToCups` 함수는 return statement의 값이 string이므로, string 값을 반환할 것이 분명히 예측됩니다. 따라서 `liquidAmount` 역시 string 값이 되어야 하는데 number로 변수를 선언했으므로 타입 에러가 나타납니다.

​    

## Return type annotation

또한, type annotation을 사용하면 함수의 리턴 값에 대해서도 더 분명하게 타입 선언을 해줄 수 있습니다.

```typescript
function createGreeting(name?: string): string {
  if (name) {
    return `Hello, ${name}!`;
  }
 
  return undefined;
  //Typescript Error: Type 'undefined' is not assignable to type 'string'.
};
```

함수의 `()` 바로 뒤에 `: type`을 설정하면, 함수의 반환 값의 타입을 지정해줄 수 있습니다.

뿐만 아니라 Arrow function에도 마찬가지로 리턴 값에 대한 타입을 지정해줄 수 있습니다.

```typescript
const createArrowGreeting = (name?: string): string => {
    if (name) {
        return `Hello, ${name}!`;
    }
 
    return undefined;
    // Typescript Error: Type 'undefined' is not assignable to type 'string'.
};
```

​    

## Void return type

함수에 특별한 이유가 없는 한, return type을 type annotation으로 명시해주는 것이 좋은 습관입니다. 다만, 따로 리턴하는 것이 없는 함수에 대해서는 `void`를 사용해 type annotation을 해주는 것이 적절합니다.

```typescript
function logGreeting(name:string): void{
  console.log(`Hello, ${name}!`)
}
```

​    

## Documentation comments

```typescript
/**
* This is a documentation comment
*/
```

함수에 대한 설명을 등록하고 마우스 호버 등을 통해 이를 확인하고 싶다면 documentation comments 기능을 활용합니다.

```typescript
  /**
   * Returns the sum of two numbers.
   *
   * @param x - The first input number
   * @param y - The second input number
   * @returns The sum of `x` and `y`
   *
   */
  function getSum(x: number, y: number): number {
    return x + y;
  }
}
```

위와 같이, 원하는 함수 위에 documentation comment를 등록하면 함수에 대한 설명을 입력할 수 있습니다. 또한, `@param`, `@returns` 등의 special tags를 활용하면, 함수의 특정 요소를 강조하는 comment를 입력할 수 있습니다.

​    

## Reference

[Codecademy - TypeScript](https://www.codecademy.com/learn/learn-typescript)