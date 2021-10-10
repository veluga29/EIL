## 1. Array

Array의 타입을 정하는 것은 앞서 진행한 primitive types와는 조금 다릅니다. 자바스크립트의 array는 다양한 타입의 데이터가 array의 요소로서 공존할 수 있기 때문입니다. 따라서, array의 타입을 알아낸다는 것은 각각의 element의 타입을 추적한다는 의미가 됩니다.

기존 자바스크립트에서는 array의 타입을 추적하는 작업이 상당히 번거롭지만, 타입스크립트는 이를 간편하게 해결해줍니다.

​    

### Array type annotation

Array의 타입을 type annotation으로 미리 정할 수 있습니다. Array type annotation 방법은 두 가지 존재합니다.

```typescript
let names: string[] = ['Danny', 'Samantha'];
```

먼저, element 타입을 기존의 type annotation 방법처럼 지정하고 `[]`를 바로 뒤에 붙여주는 방식입니다. 위의 경우, element의 타입이 string인 array가 만들어집니다.

```typescript
let names: Array<string> = ['Danny', 'Samantha'];
```

또 다른 방법은 `Array<T>` 문법을 사용하는 것입니다. 이 역시 앞선 코드와 동일하게 array의 element 타입을 string으로 지정합니다.

이렇게 type annotation이된 array는 array를 생성할 때의 element가 지정한 타입과 다르거나 array에 새로 추가하는 element의 타입이 지정한 타입과 다를 때, 타입 에러를 보여줍니다.

​     

### Multi-dimensional array

다차원 array를 다룰 때는 차원수에 대응하여 `[]`를 type annotation으로 더 붙여주면됩니다.

```typescript
let arr: string[][] = [['str1', 'str2'], ['more', 'strings']];
```

예를 들어, `string[][]`의 경우 `(string[])[]`을 요약한 것입니다. 즉, 모든 element가 `string[]` 타입을 가지는 array라고 해석하면 됩니다.

```typescript
let names: string[] = []; // No type errors.
let numbers: number[] = []; // No type errors.
names.push('Isabella');  
numbers.push(30);
```

이 때, 빈 array `[]`는 어떤 array 타입의 값으로 할당되어도 문제없이 실행됨을 유의합니다.

​    

### Tuple

자바스크립트의 array는 앞서 말했듯 다양한 타입의 요소들이 섞여서 구성될 수 있습니다. 타입스크립트에서는 이렇게 다양한 타입의 element로 구성된 array를 tuple이라고 부르며, 새로운 자료형으로서 다룹니다. 

```typescript
let ourTuple: [string, number, string, boolean] = ['Is', 7 , 'our favorite number?' , false]; 
```

Tuple은 위와 같이 `[]`안에 원하는 순서대로 데이터 타입을 나열함으로써 구현합니다. 이로 인해, tuple은 tuple 내 element 타입의 순서와 tuple의 길이가 미리 고정되게 됩니다.

```typescript
let tup: [string, string] = ['hi', 'bye'];
let arr: string[] = ['there','there'];
tup = ['there', 'there']; // No Errors.
tup = arr; // Type Error! An array cannot be assigned to a tuple.
```

자바스크립트에서는 array나 tuple이 모두 동일하게 간주됩니다. 하지만, 타입스크립트에서는 두 자료형이 다르게 취급되며, 심지어 element들이 동일한 타입을 가졌을지라도 tuple 변수에 array를 할당하는 것이 불가능합니다.

​    

### Array type inference

타입스크립트는 변수에 초기화된 value 혹은 return statement를 보고 해당 변수의 타입을 추론합니다. 이는 array가 value로 주어져도 마찬가지입니다.

```typescript
let examAnswers= [true, false, false];
```

다만, 이때 `examAnswers`의 타입이 `boolean[]`인 array인지 `[boolean, boolean, boolean]`인 tuple인지 헷갈립니다. 타입스크립트는 이에 대해 항상 `boolean[]` array로서 타입을 추론합니다. 길이 및 타입 순서의 고정 등 제약이 많은 tuple보다 array가 더 자유로운 타입이기 때문입니다.

```typescript
let tup: [number, number, number] = [1,2,3];
let concatResult = tup.concat([4,5,6]); // concatResult has the value [1,2,3,4,5,6].
```

위과 같이, tuple과 array를 concatenation하는 상황에서도 `concatResult`는 array 타입으로 추론됩니다.

따라서, 타입스크립트의 type inference에서는 tuple로 추론되는 경우가 없습니다. Tuple을 사용하고 싶다면, 앞서 확인한 type annotation을 사용해야만 합니다.

​    

### Rest parameters type annotation

```typescript
function addPower(p: number, ...numsToAdd: number[]): number{
    /* rest of function */
}
```

Rest parameters는 고정되지 않은 다수의 인자를 array로서 받습니다. 따라서, array 타입으로서 인자에 기존 방식대로 type annotation을 줄 수 있습니다.

​    

### Spread syntax with tuple

자바스크립트의 spread syntax는 tuple과 매우 궁합이 잘 맞습니다.

```typescript
function gpsNavigate(startLatitudeDegrees:number, startLatitudeMinutes:number, startNorthOrSouth:string, startLongitudeDegrees: number, startLongitudeMinutes: number, startEastOrWest:string, endLatitudeDegrees:number, endLatitudeMinutes:number , endNorthOrSouth:string, endLongitudeDegrees: number, endLongitudeMinutes: number,  endEastOrWest:string) {
  /* navigation subroutine here */
}
```

예를 들어, 위의 함수를 두 가지 위치 정보를 받아 경로를 찾는 함수라고 생각해봅시다. 많은 수의 인자를 받는 함수이기 때문에 `gpsNavigate(40, 43.2, 'N', 73, 59.8, 'W', 25, 0, 'N', 71, 0, 'W')`와 같이 호출하게 됩니다. 이는 가독성이 떨어지기에, 다음과 같이 두 가지 tuple 변수로 나눠봅시다.

```typescript
let codecademyCoordinates: [number, number, string, number, number, string] = [40, 43.2, 'N', 73, 59.8, 'W'];
let bermudaTCoordinates: [number, number, string, number, number, string] = [25, 0 , 'N' , 71, 0, 'W'];
```

그리고 spread syntax를 사용해 조금 더 가독성을 높여 호출해봅시다.

```typescript
gpsNavigate(...codecademyCoordinates, ...bermudaTCoordinates);
// And by the way, this makes the return trip really convenient to compute too:
gpsNavigate(...bermudaTCoordinates, ...codecademyCoordinates);
// If there is a return trip . . . 
```

​    

## 2. Complex type

### Enum

관련있는 상수들의 집합을 열거형(Enum)이라고 합니다. 앞서 살펴본 타입들은 해당 타입의 값들이 다양하게 무한한 경우의 수로 존재할 수 있지만, enums는 값의 경우의 수가 제한됩니다. 즉, 내가 원하는 경우의 수 값들로만 타입을 구성하고 enumerate하고 싶을 때 enum이 적합합니다.

```typescript
enum Direction {
  North,
  South,
  East,
  West
}
```

위와 같이 동서남북을 정의하고 싶을 때, enum을 사용할 수 있습니다. Enum에서 각각의 enum variable(∝key)들에는 number 타입을 가지는 숫자 값(∝value)이 순서대로 자동 대응되게 됩니다. 즉, `Direction.North`, `Direction.South`, `Direction.East`, `Direction.West`는 각각 0, 1, 2, 3의 값들이 대응됩니다.

```typescript
let whichWayToArcticOcean: Direction;
whichWayToArcticOcean = Direction.North; // No type error.
whichWayToArcticOcean = Direction.Southeast; // Type error: Southeast is not a valid value for the Direction enum.
whichWayToArcticOcean = West; // Wrong syntax, we must use Direction.West instead. 
```

Enum으로 만든 custom type은 기존 방식처럼 type annotation을 그대로 사용할 수 있습니다. 그리고 enum으로 type annotation한 변수는 위와 같이 `Direction` enum에서 `.`을 통해 접근가능한 값들만 변수에 할당할 수 있게 됩니다.

```typescript
let whichWayToAntarctica: Direction;
whichWayToAntarctica = 1; // Valid TypeScript code.
whichWayToAntarctica = Direction.South; // Valid, equivalent to the above line.
```

만일 enum value가 number type이라면, 위의 `whichWayToAntarctica = 1;` 같이 임의의 enum value를 직접 할당하는 것도 가능합니다. (Enum의 value로는 number 혹은 string 타입 두 가지 경우가 가능합니다. 뒤에서 더 설명하겠습니다.)

```typescript
enum Direction {
  North = 7,
  South,
  East,
  West
}
```

위와 같이 `North`가 7부터 시작하는 enum도 만들 수 있습니다. 이 때, `South`, `East`, `West`는 각각 8, 9, 10으로 1씩 자동으로 증가하며 대응됩니다.

```typescript
enum Direction {
  North = 8,
  South = 2,
  East = 6,
  West = 4
}
```

만일 모든 값에 각기 다른 값을 대응시키고 싶다면, 위 코드처럼 빠짐없이 명시해주면 됩니다.

​    

### String Enum VS Numeric Enum

Enum은 number 혹은 string 타입에 한해서 자신의 value를 가질 수 있습니다. 앞서 살펴본 enum은 number 타입의 enum value를 가지는 numeric enum이었습니다. 이와 대조적으로, string 타입의 enum value를 가지는 string enum을 살펴봅시다.

```typescript
enum DirectionNumber { North, South, East, West }
enum DirectionString { North = 'NORTH', South = 'SOUTH', East = 'EAST', West = 'WEST' }
```

자동으로 number 타입 값이 할당되던 numeric enum과 달리, string enum은 직접 string 값을 명시해줘야 합니다. String 타입 enum value는 어떤 것이든 올 수 있지만, 관례적으로 enum variable의 대문자 형태를 사용하는 것이 일반적입니다. 덕분에 에러메시지나 로그에 정보가 담기기 때문입니다. 이러한 정보적 측면에서, enum은 항상 string enum으로 사용할 것이 권장됩니다.

```typescript
let whichWayToAntarctica: DirectionString;
whichWayToAntarctica = '\ (•◡•) / Arbitrary String \ (•◡•) /'; // Type error!
whichWayToAntarctica = 'SOUTH'; // STILL a type error!
whichWayToAntarctica = DirectionString.South; // The only allowable way to do this.
```

String enum이 권장되는 또 하나의 이유는 enum 정의 이후에는 임의의 string 값 할당이 불가능함에 있습니다. 위와 같이 enum으로 type annotation된 변수에 string 값을 할당하면, enum에 이미 존재하는 enum value임에도 type error가 발생합니다. 즉, `whichWayToAntarctica = DirectionString.South;`처럼 오직 `.`을 통해 접근한 값만 할당 가능합니다. 임의의 값을 마음대로 할당가능한 numeric enum가 대조되는 부분입니다.

```typescript
let whichWayToAntarctica: DirectionNumber;
whichWayToAntarctica = 1; // Valid TypeScript code.
whichWayToAntarctica = DirectionNumber.South; // Valid, equivalent to the above line.
whichWayToAntarctica = 943205; // Also, valid TypeScript code!!
```

​    

### Object

```typescript
let aPerson: {name: string, age: number};
```

Object 역시 type annotation으로 사용할 수 있습니다. 위의 코드는 object type annotation하는 syntax의 예시입니다. Object 리터럴과 매우 비슷해 보이지만, 각 property의 value 위치에 type이 명시되어 있습니다.

```typescript
aPerson = {name: 'Aisle Nevertell', age: "wouldn't you like to know"}; // Type error: age property has the wrong type.
aPerson = {name: 'Kushim', yearsOld: 5000}; // Type error: no age property. 
aPerson = {name: 'User McCodecad', age: 22}; // Valid code. 
```

그리고 실제로 각각의 property의 이름과 type이 동일한 object가 아니라면 타입 에러를 띄웁니다.

```typescript
let aCompany: {
  companyName: string, 
  boss: {name: string, age: number}, 
  employees: {name: string, age: number}[], 
  employeeOfTheMonth: {name: string, age: number},  
  moneyEarned: number
};
```

Object의 큰 장점은 property에 type 제한이 없다는 점입니다. Object의 property에는 enum, array 혹은 또 다른 object까지 다양하고 자유롭게 type을 명시할 수 있습니다.

​    

### Type alias

만일 object나 tuple 타입 같은 복잡한 타입이 동일하게 자주 반복된다면, 해당 타입에 따로 임의의 이름을 정해 효율을 높일 수 있습니다. `type <alias name> = <type>` 형태로 원하는 타입에 별칭을 부여합니다.

```typescript
let aCompany: { 
  companyName: string, 
  boss: { name: string, age: number }, 
  employees: { name: string, age: number }[], 
  employeeOfTheMonth: { name: string, age: number },  
  moneyEarned: number
};
```

위와 같이 반복적으로 동일한 타입이 자주 나오는 경우, 다음과 같이 type alias를 사용해 반복을 줄입니다.

```typescript
type Person = { name: string, age: number };
let aCompany: {
  companyName: string, 
  boss: Person, 
  employees: Person[], 
  employeeOfTheMonth: Person,  
  moneyEarned: number
};
```

Type alias에서 유의할 점은 type alias는 단순히 별칭일 뿐, 그 자체로는 타입이 아니라는 것입니다. 따라서 아래와 같은 코드는 type alias의 이름이 달라도 내부의 타입은 동일하기 때문에, 타입 에러를 일으키지 않습니다.

```typescript
type MyString = string; 
type MyOtherString = string;
let firstString: MyString = 'test';
let secondString: MyOtherString = firstString; // Valid code.
```

​    

### Function type

자바스크립트의 함수는 변수에 담길 수 있습니다. 그리고 타입스크립트는 함수가 담기는 변수에 함수 타입을 적용할 수 있습니다.

```typescript
type StringsToNumberFunction = (arg0: string, arg1: string) => number;
```

위 코드는 마치 arrow function과 유사하지만, 함수가 아니라 함수 타입을 정의한 것입니다. 위의 문법을 사용해 각각의 파라미터에 타입을 지정할 수 있고, `=>` 뒤에는 return 타입을 지정합니다. 

참고로 `type StringsToNumberFunction`는 type alias에 해당합니다.

```typescript
let myFunc: StringsToNumberFunction;
myFunc = function(firstName: string, lastName: string) {
  return firstName.length + lastName.length;
};
 
myFunc = function(whatever: string, blah: string) {
  return whatever.length - blah.length;
};
// Neither of these assignments results in a type error.
```

앞서 정의한 함수 타입을 `myFunc` 변수에 적용했습니다. 이후 `myFunc`에 할당된 함수들은 모두 `StringsToNumberFunction` 타입에 어긋나지 않기 때문에, 타입에러가 일어나지 않습니다. 이 때, 함수 타입에 설정한 `arg0`, `arg1`과 실제 파라미터의 이름은 달라도 괜찮습니다.

```typescript
type StringToNumberFunction = (string)=>number; // NO
type StringToNumberFunction = arg: string=>number; // NO NO NO NO
```

함수 타입에서 유의할 점은 파라미터의 이름을 안 쓴다거나 파라미터를 둘러싸는 `()`를 빼먹어서는 안되는 것입니다. 함수 타입에서는 파라미터가 하나여도 `()`를 반드시 써줘야 합니다.

```typescript
type OperatorFunction = (arg0: number, arg1: number) => number;

// Math Tutor Function That Accepts a Callback
function func(operationCallback: OperatorFunction) {
  operationCallback();
}
```

특히, 콜백 함수를 인자로 받을 때 이러한 함수 타입들은 더욱 유용할 것입니다.

​    

### Generic type

제네릭(Generic)은 내부에서 사용할 데이터 타입을 외부에서 지정하는 기법을 의미합니다. 예를 들어, 클래스를 정의 할 데이터 타입을 확정하지 않고 인스턴스를 생성할 때 데이터 타입을 지정하는 것은 제네릭에 한 예입니다. 앞서 봤던, array element에 타입을 적용하는 `Array<T>` 문법도 제네릭의 예시에 해당합니다.

```typescript
type Family<T> = {
  parents: [T, T], mate: T, children: T[]
};
```

Object에도 위와 같이 제네릭을 적용할 수 있습니다. `Family<T>` 자체로는 아직 type annotation을 할 수 없습니다. `T`라는 타입 변수 자리에 원하는 타입을 대체했을 때, 비로소 type annotation으로 기능할 수 있습니다. 이 때, 식별자 `T`는 관습적으로 쓰이는 것이므로 임의로 바꿔도 괜찮습니다.

```typescript
let aStringFamily: Family<string> = {
  parents: ['stern string', 'nice string'],
  mate: 'string next door', 
  children: ['stringy', 'stringo', 'stringina', 'stringolio']
}; 
```

위와 같이 타입 변수 `T`를 `string`으로 대체하면, 타입 내 원래의 `T` 자리는 모두 `string`으로 대체되기 때문에, 위 코드는 결과적으로 에러 없이 잘 동작합니다.

​    

### Generic function

함수의 리턴 타입을 설정할 때도 제네릭은 유용하게 사용됩니다.

```typescript
function getFilledArray<T>(value: T, n: number): T[] {
  return Array(n).fill(value);
}
```

위와 같이 array를 생성하는 함수는 array에 삽입되는 요소의 타입이 `value`의 값에 따라 달라지기 때문에, 리턴되는 array의 타입을 정의하기가 어렵습니다.

따라서, 제네릭을 적용하여 `T`로 리턴 타입 정의를 용이하게 합니다.

```typescript
getFilledArray<string>('cheese', 3)
```

제네릭 함수의 호출은 위와 같이 원하는 타입을 `<>` 안에 명시하여 실행합니다.

`getFilledArray<string>`의 경우는 결과적으로 type annotation `(value: string, n: number): string[]`와 동일해집니다. 즉, `T`가 함수 내부의 type annotation에서 적용 가능해집니다.

​    

## Reference

[Codecademy - TypeScript](https://www.codecademy.com/learn/learn-typescript)
