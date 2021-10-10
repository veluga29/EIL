## Interface

타입스크립트에서 타입을 정의하는 방법은 다양합니다.

```typescript
type Mail = {
  postagePrice: number;
  address: string;
}
 
const catalog: Mail = ...
```

기존처럼 `type`을 사용해 정의할 수도 있습니다.

```typescript
interface Mail {
  postagePrice: number;
  address: string;
}
 
const catalog: Mail = ...
```

그런데 타입스크립트에서 객체의 타입을 정의하는데 자주 사용되는 또 하나의 방법은 `interface`를 사용하는 것입니다. `type`과 `interface`는 문법적인 측면에서 `=` 사용의 차이가 있지만, 타입을 강제하는 기능은 동일합니다.

그렇다면 `interface`는 어디에 사용하는 것일까요?

`type`은 object 뿐만 아니라 primitive 타입을 포함한 모든 타입을 정의하는데 사용할 수 있는 반면, `interface`는 object 타입 정의에만 사용할 수 있습니다. 마치 설계도와 같은 느낌이 녹아 있는 `interface`는 제약이 있다는 점에서 코드를 일관성 있게 작성하도록 도와주기 때문에, 객체 지향 프로그램을 작성할 때는 `interface`를 주로 사용합니다.

​    

## Interfaces and class

Interface와 class는 궁합이 잘 맞습니다. Interface는 object의 타입을 정의하는 키워드이고 class는 object로 프로그래밍하는 방법이기 때문입니다. 

```typescript
interface Robot {
  identify: (id: number) => void;
}
 
class OneSeries implements Robot {
  identify(id: number) {
    console.log(`beep! I'm ${id.toFixed(2)}.`);
  }
 
  answerQuestion() {
    console.log('42!');
  }
}
```

`interface`는 class / object에 타입을 적용할 수 있습니다. 특히, class에 타입을 적용할 때에는 `implements` 키워드를 사용합니다.

위 코드는 `OneSeries` 클래스에 `implements` 키워드를 사용해 `Robot` 타입을 적용하는 과정입니다. `Robot` 타입이 적용된 `OneSeries`는 인터페이스에 명시된 대로 `identify` 메서드를 가져야 하며, 명시된 것만 지켰다면 이외로 추가적인 `answerQuestion` 메서드를 가지는 것도 가능합니다.

​    

## Deep nested type

```typescript
class OneSeries implements Robot {
  about;
 
  constructor(props: { general: { id: number; name: string; } }) {
    this.about = props;
  }
 
  getRobotId() {
    return `ID: ${this.about.general.id}`;
  }
}
```

Class `OneSeries`는 nested된 object 타입을 가지는 `about` 프로퍼티와 `getRobotId` 메서드를 가집니다. 이러한 nested된 object 타입을 표현하고 싶다면, `interface` `Robot`은 다음과 같이 작성하면 됩니다.

```typescript
interface Robot {
  about: {
    general: {
      id: number;
      name: string;
    };
  };
  getRobotId: () => string;
}
```

타입스크립트는 무한히 nested된 object 타입을 표현할 수 있습니다!

​    

## 타입 구성 분리하기

```typescript
interface About {
  general: {
    id: number;
    name: string;
    version: {
      versionNumber: number;
    }
  }
}
```

위와 같이 더욱 깊게 nested되는 object 타입일수록 가독성은 떨어집니다. 또한, `About` 타입에서도 `version`만 필요한 상황이 있을 수 있습니다. 따라서, 일정한 정도로 각각 따로 `interface`를 만들어 함께 사용하는 것이 효과적일 수 있습니다.

```typescript
interface About {
  general: General;
}
 
interface General {
  id: number;
  name: string;
  version: Version;
}
 
interface Version {
  versionNumber: number;
}
```

앞선 복잡했던 `interface` 코드를 가독성 높은 재사용가능한 코드로 변형했습니다. 코드는 조금 길어졌지만, 더욱 큰 프로그램에서는 이러한 형태로 코드를 작성하는 것이 훨씬 유리합니다.

​    

## Extending interface

때때로 어떤 타입의 모든 프로퍼티와 메서드들을 복사해서 다른 타입에 가져와야 할 때도 있습니다. 이 때 `extends`가 유용합니다.

```typescript
interface Shape {
  color: string;
}
 
interface Square extends Shape {
  sideLength: number;
}
 
const mySquare: Square = { sideLength: 10, color: 'blue' };
```

`Square`는 `extends` 키워드를 사용해 `Shape`의 모든 프로퍼티를 복사해서 가져옵니다. 실제로 `mySquare`에서는 `sideLength` 프로퍼티 뿐만 아니라 `color` 프로퍼티를 가져도 에러가 나지 않습니다.

​    

## Index signature

외부의 API나 소스로부터 데이터를 받아오는 경우, 특정 객체의 프로퍼티 이름이 정확히 무엇인지 알 수 없는 상황이 생깁니다. 이 때, 해당 프로퍼티들을 받는 변수를 임의의 이름으로 하나 설정해 처리할 수 있습니다. 이를 index signature라고 합니다.

```typescript
{
  '40.712776': true;
  '41.203323': true;
  '40.417286': false;
}
```

예를 들어, 위와 같은 데이터를 map API query에 대한 response로 받았다고 가정해봅시다. String 타입으로 이루어진 각각의 프로퍼티들은 위도를 나타냅니다. 다만, 이러한 프로퍼티들은 개발자 입장에서 정확히 이름을 알기가 어렵습니다.

```typescript
interface SolarEclipse {
  [latitude: string]: boolean;
} 
```

따라서, 위와 같이 `[latitude: string]`라는 index signature를 정의해주면, response로 받는 데이터에 존재하는 모든 프로퍼티들의 타입을 하나로 정의할 수 있습니다. 위의 경우 모든 프로퍼티의 이름은 string 타입으로, 그 값은 boolean 타입으로 정의됩니다. `latitude`는 개발자가 임의로 설정한 이름임을 유의합니다.

​    

## Optional type member

어떤 함수나 클래스를 만들 때, optional argument 설정은 자유롭습니다. 이는 `interface`로 타입을 정의할 때도 마찬가지입니다. `interface`에서도 타입 멤버들에 대해 optional 속성을 설정해 줄 수 있습니다.

```typescript
interface OptionsType {
  name: string;
  size?: string;
}
 
function listFile(options: OptionsType) {
  let fileName = options.name;
 
  if (options.size) {
    fileName = `${fileName}: ${options.size}`;
  }
 
  return fileName;
}
```

위의 `size` 프로퍼티는 optional한 프로퍼티입니다. 프로퍼티 이름과 `:`사이에 `?`가 존재한다면, 해당 프로퍼티는 optional 프로퍼티로 간주됩니다. 따라서, 위 코드에서는 `size` 프로퍼티를 사용하기 전에 `if (options.size)` 조건문을 사용해 `size` 프로퍼티의 존재 여부를 먼저 확인하고 사용해야 합니다.

```typescript
listFile({ name: 'readme.txt' })
```

`size` 프로퍼티가 optional하기 때문에, 위와 같이 `size` 프로퍼티가 없는 객체를 인자로 사용해도 에러를 일으키지 않습니다.

​    

## Reference

[Codecademy - TypeScript](https://www.codecademy.com/learn/learn-typescript)
