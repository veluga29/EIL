## Type narrowing

타입스크립트는 자신의 소스코드를 자바스크립트 코드로 컴파일하는 단계에서, 타입 체크를 하여 개발자에게 알림을 줍니다. 이러한 컴파일 단계에서의 타입 체크는 매우 유용합니다. 그러나 타입스크립트는 더 많은 것을 제공해줄 능력이 있습니다.

타입스크립트는 코드의 주변 맥락을 확인하여 런타임시 어떻게 동작할지 파악하고, 이에 따라 변수의 구체적인 타입을 추론하여 알려줍니다! 이를 type narrowing이라고 합니다. 특히, 변수가 union을 통해 다양한 타입의 가능성을 내재하고 있을 때, type narrowing은 빛을 발합니다.

```typescript
function formatDate(date: string | number) {
  // date can be a number or string here
 
  if (typeof date === 'string') {
    // date must be a string here
  }
}
```

위와 같은 코드에서 `date` 인자는 string 타입도 number 타입도 가능합니다. 이 때, `if (typeof date === 'string')`같은 type guard를 사용해 각각의 타입마다 따로 로직을 만들어 type narrowing 할 수 있습니다. 이는 타입스크립트의 런타임 코드 실행 맥락 파악을 통한 타입 추론 능력 덕분입니다!

​    

## Type guard

```typescript
function formatDate(date: string | number) {
  // date can be a number or string here
 
  if (typeof date === 'string') { 
    // date must be a string type
  }
}
```

타입스크립트의 type narrowing은 type guard를 통해 진행됩니다. Type guard는 변수의 구체적인 타입을 체크하는 표현식을 가리킵니다. 일반적으로 `typeof`가 많이 활용됩니다. 위에서 `if (typeof date === 'string')` 부분이 type guard에 해당됩니다.

​    

## `in ` as type guard

때때로 특정 프로퍼티 혹은 메서드가 해당 타입에 존재하는지 확인하고 싶은 경우가 있습니다. 이 때, `in` operator를 사용할 수 있습니다. `in`은 특정 프로퍼티가 객체 자체에 혹은 해당 객체의 프로토타입 체인 내에 존재하는지 확인해줍니다.

```typescript
type Tennis = {
  serve: () => void;
}
 
type Soccer = {
  kick: () => void;
}
 
function play(sport: Tennis | Soccer) {
  if ('serve' in sport) {
    return sport.serve();
  }
 
  if ('kick' in sport) {
    return sport.kick();
  }
}
```

그리고 `in`은 type guard로서 사용할 수 있습니다. 위의 `if ('serve' in sport)`에서는 특정 프로퍼티에 존재 여부가 타입스크립트에게 단서를 주어 type narrowing이 이루어집니다. 위 코드의 경우, 만일 `'serve'`가 `sport`에 존재한다면, `sport`는 `Tennis` 타입일 것이 확정되기 때문에 `if ('serve' in sport)` 구문 내에서는 `sport`를 `Tennis` 타입 변수로 간주하고 코드를 짜도 무방합니다. 즉, 타입스크립트가 에러를 띄우지 않습니다.

​    

## Narrowing with else

만일 if 조건문이 type guard로 쓰였다면, 이에 대응하는 else 문은 if 문과 정확히 반대되는 type guard로서 기능합니다. 즉, if 문에서 체크한 타입 이외의 가능한 타입들은 모두 else 문에서 고려하게 됩니다.

```typescript
function formatPadding(padding: string | number) {
  if (typeof padding === 'string') {
    return padding.toLowerCase();
  } else {
    return `${padding}px`;
  }
}
```

예를 들어, 위 if 문에서 string 타입에 대한 로직을 작성했기 때문에, else 문은 number 타입에 대한 로직을 자동으로 담당하게 됩니다.

​    

## Narrowing After a Type Guard

사실 else 문을 사용하지 않아도 else 문과 똑같은 type narrowing을 사용할 수 있습니다. Type guard인 if 문이 끝난 이후 나오는 코드들은 나머지 가능한 타입들에 대한 코드로 자동으로 상정됩니다.

```typescript
type Tea = {
  steep: () => string;
}
 
type Coffee = {
  pourOver: () => string;
} 
 
function brew(beverage: Coffee | Tea) {
  if ('steep' in beverage) {
    return beverage.steep();
  }
 
  return beverage.pourOver();
}
```

예를 들어, 위의 if 문 내에서는 `beverage`가 Tea 타입을 가질 것입니다. 반면, if 문이 끝나고 나온 `return beverage.pourOver();` 코드에서는 beverage가 당연히 `Coffee` 타입일 것이기 때문에, 타입스크립트는 오류를 내지 않습니다.

​    

## Reference

[Codecademy - TypeScript](https://www.codecademy.com/learn/learn-typescript)