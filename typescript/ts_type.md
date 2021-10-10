## TypeScript

타입스크립트는 2012년 마이크로소프트가 발표한 기존 자바스크립트에 정적 타입 문법을 추가한 프로그래밍 언어입니다. 자바스크립트의 슈퍼셋(Superset)이기 때문에 타입스크립트 컴파일러 혹은 바벨(Babel)을 이용해 자바스크립트 코드로 변환되어 실행됩니다.

동적 타입의 인터프리터 언어인 자바스크립트와 달리, 타입스크립트는 정적 타입의 컴파일 언어이며 미리 타입을 결정하기 때문에 실행 속도가 매우 빠릅니다. 다만, 매 코드 작성시 타입을 설정하는 번거로움과 더불어, 늘어가는 코드량으로 인해 컴파일 속도는 오래걸린다는 단점이 함께 합니다. 

그러나 타입스크립트의 가장 큰 장점은 코드 작성단계에서 타입을 체크해 에러를 사전에 방지할 수 있다는 점입니다. 또한, IDE의 코드 자동 완성을 지원하기 때문에 개발 생산성을 크게 향상시키는 이점도 있습니다. 

​    

## 타입 추론(Type inference)

타입 추론(Type inference)은 타입스크립트가 변수의 데이터 타입을 정할 때 처음 정의할 때 할당한 값을 분석하여 타입을 추론해 지정하는 방식입니다.

```typescript
let order = 'first';
 
order = 1;
```

따라서 위와 같이 처음  `order`를 정의할 때 String 값으로 정의했다면, `order`에는 `1`과 같은 Number 타입의 값이 재할당될 수 없습니다.

```typescript
"MY".toLowercase();
// Property 'toLowercase' does not exist on type '"MY"'.
// Did you mean 'toLowerCase'?
```

또한, 타입스크립트는 유추한 해당 타입의 shape 역시 확인하여 위와 같이 메서드 이름 오타로 인한 버그도 쉽게 잡아낼 수 있습니다.

​    

## Any

만일 변수에 값을 할당하지 않고 선언만 한다면, 해당 변수는 `any` 타입을 가집니다.

```typescript
let onOrOff;
 
onOrOff = 1;
onOrOff = false;
```

위의 `onOrOff`는 선언만 되었기 때문에, `any` 타입을 가집니다. 이 경우, 변수의 값이 기존과 다른 타입의 값으로 재할당되어도 타입스크립트는 오류를 일으키지 않습니다.

​    

## Type annotation

변수에 값을 할당하지 않고 선언만 했을 때, 해당 변수가 `any` 타입이 아니라 특정 타입을 명확히 가지길 원할 수 있습니다. 이 때, type annotation을 사용합니다.

```typescript
let mustBeAString : string;
mustBeAString = 'Catdog';
 
mustBeAString = 1337;
// Error: Type 'number' is not assignable to type 'string'
```

위와 같이, `let mustBeAString : string;`으로 String 타입을 명확히 지정해두면, String 이외 타입의 원치 않는 데이터 할당을 막을 수 있습니다.

​    

## Reference

[Codecademy - TypeScript](https://www.codecademy.com/learn/learn-typescript)

[타입스크립트 핸드북](https://joshua1988.github.io/ts/why-ts.html#%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%9E%80)

[활용도가 높아지는 웹 프론트엔드 언어, 타입스크립트(TypeScript)](https://www.samsungsds.com/kr/insights/TypeScript.html)

