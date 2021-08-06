# Object

Javascript의 data type은 6개의 primitive data type(string, number, boolean, null, undefined, symbol)과 1개의 object data type으로 구성되어 있습니다. Javascript는 객체지향 언어이고 6개의 primitive data type도 객체와 같이 동작하는 특징이 있습니다. 또한, object는 mutable(변경가능한) 속성을 가집니다.

​    

## Syntax

Object는 `{}`를 통해 구현됩니다. `{}` 안에는 unordered data를 `key`-`value` pair로 삽입합니다. `value`의 경우 어떤 data type이 와도 괜찮습니다. 반면에, `key`의 타입은 string이어야 합니다. 다만, `key`의 경우 특별한 특수문자를 집어넣는 것이 아니라면 quotation mark 없이 사용해도 string으로 자동 인식됩니다.

```javascript
// An object literal with two key-value pairs
let spaceship = {
  'Fuel Type': 'diesel',
  color: 'silver'
};
```

​    

## Property

Object에 저장된 함수가 아닌 data는 property라고 부릅니다. Property에 접근할 때는 `.`이 사용됩니다. 만일 object 내에 없는 property에 접근한 경우에는 `undefined`가 반환됩니다.

```javascript
let spaceship = {
  homePlanet: 'Earth',
  color: 'silver'
};
spaceship.homePlanet; // Returns 'Earth',
spaceship.color; // Returns 'silver',
```

또 다른 방법은 `[]`을 사용하는 것입니다. 원하는 `key`를 `[]`안에 넣으면 object에서 해당하는 property에 접근합니다. `[]`는 특수문자가 포함된 `key` string에 특히 유용합니다.

```javascript
let spaceship = {
  'Fuel Type': 'Turbo Fuel',
  'Active Duty': true,
  homePlanet: 'Earth',
  numCrew: 5
};
spaceship['Active Duty'];   // Returns true
spaceship['Fuel Type'];   // Returns  'Turbo Fuel'
spaceship['numCrew'];   // Returns 5
spaceship['!!!!!!!!!!!!!!!'];   // Returns undefined
```

​    

## Add, update and delete

`[]`, `.`와 `=`를 사용하면, object에 새로운 property를 추가하거나 기존 property를 수정할 수 있습니다. 또한, `const` 변수에 담긴 object여도 해당 object 안의 property를 추가하거나 수정할 수 있습니다.

```javascript
const spaceship = {type: 'shuttle'};
spaceship = {type: 'alien'}; // TypeError: Assignment to constant variable.
spaceship.type = 'alien'; // Changes the value of the type property
spaceship.speed = 'Mach 5'; // Creates a new key of 'speed' with a value of 'Mach 5'
```

Object 내의 property를 삭제하는 방법은 `delete` 키워드를 사용하는 것입니다. 역시 `const` 변수에 담긴 object여도 내부의 property 삭제가 가능합니다.

```javascript
const spaceship = {
  'Fuel Type': 'Turbo Fuel',
  homePlanet: 'Earth',
  mission: 'Explore the universe' 
};
 
delete spaceship.mission;  // Removes the mission property
```

​    

## Method

Object 내에 저장된 데이터가 함수라면, 해당 데이터는 method라고 부릅니다. Method는 `key`에 method 이름을, `value`에 익명 함수를 저장함으로써 구현합니다.

```javascript
const alienShip = {
  invade: function () { 
    console.log('Hello! We have come to dominate your planet. Instead of Earth, it shall be called New Xaculon.')
  }
};
```

ES6에서 새로이 소개된 method 문법에서는 `:`과 `function` 키워드 없이도 정의할 수 있습니다.

```javascript
const alienShip = {
  invade () { 
    console.log('Hello! We have come to dominate your planet. Instead of Earth, it shall be called New Xaculon.')
  }
};
```

Method는 `.`, `()`를 사용해 호출합니다.

```javascript
alienShip.invade(); // Prints 'Hello! We have come to dominate your planet. Instead of Earth, it shall be called New Xaculon.'
```

​    

## Pass by reference

Javascript에서 object는 pass by reference로 동작합니다. Object를 담는 변수는 실제로는 해당 객체가 담겨 있는 메모리 주소를 담기 때문에, object가 함수에 인자로 전달되어 변형이 일어나면 함수 밖의 실제 object도 영향을 받아 변형됩니다.

```javascript
const spaceship = {
  homePlanet : 'Earth',
  color : 'silver'
};
 
let paintIt = obj => {
  obj.color = 'glorious gold'
};
 
paintIt(spaceship);
 
spaceship.color // Returns 'glorious gold'
```

​    

> **함수 내에서 object를 재할당하는 경우**
>
> ```javascript
> let spaceship = {
>   homePlanet : 'Earth',
>   color : 'red'
> };
> let tryReassignment = obj => {
>   obj = {
>     identified : false, 
>     'transport type' : 'flying'
>   }
>   console.log(obj) // Prints {'identified': false, 'transport type': 'flying'}
>  
> };
> tryReassignment(spaceship) // The attempt at reassignment does not work.
> spaceship // Still returns {homePlanet : 'Earth', color : 'red'};
>  
> spaceship = {
>   identified : false, 
>   'transport type': 'flying'
> }; // Regular reassignment still works.
> ```
>
> 함수의 인자로 object를 받을 때, 함수 내에서 새로운 object를 재할당을 하는 것은 기존 object에 영향을 미치지 않습니다. 
>
> 위 예에서 `obj` 파라미터는 함수내에 생성되는 로컬 변수입니다. `tryReassignment` 함수의 흐름은 파라미터 `obj`에 인자로 들어온 object의 메모리 주소가 담기고, 이에 대해 새로운 object를 할당하여 새 object의 메모리 주소가 다시 `obj`에 담기게끔 이어집니다. 하지만, 함수가 종료되면 로컬 변수였던 `obj` 역시 사라지기 때문에, 기존 `spaceship`에 담긴 object는 변형 없이 그대로 남아 있게 됩니다.

​    

## for ... in

Array의 경우 index를 통해 looping할 수 있지만, object는 `key`를 사용하기 때문에 다른 looping 수단이 필요합니다. 따라서, object looping에 대해서는 `for ... in` 구문을 사용합니다.

```javascript
let spaceship = {
  crew: {
    captain: { 
      name: 'Lily', 
      degree: 'Computer Engineering', 
      cheerTeam() { console.log('You got this!') } 
    },
    'chief officer': { 
      name: 'Dan', 
      degree: 'Aerospace Engineering', 
      agree() { console.log('I agree, captain!') } 
    },
    medic: { 
      name: 'Clementine', 
      degree: 'Physics', 
      announce() { console.log(`Jets on!`) } },
    translator: {
      name: 'Shauna', 
      degree: 'Conservation Science', 
      powerFuel() { console.log('The tank is full!') } 
    }
  }
}; 
 
// for...in
for (let crewMember in spaceship.crew) {
  console.log(`${crewMember}: ${spaceship.crew[crewMember].name}`);
}
```

​    

## `this` keyword

`this` 키워드는 calling object를 나타내며, object의 method 내에서 property에 접근할 때는 `this` 키워드를 사용합니다. 여기서 calling object란 해당 method가 속한 객체를 말합니다.

```javascript
const goat = {
  dietType: 'herbivore',
  makeSound() {
    console.log('baaa');
  },
  diet() {
    console.log(this.dietType);
  }
};
 
goat.diet(); 
// Output: herbivore
```

예를 들어, `diet()` method에서 `dietType` property에 접근하기 위해서는 반드시 `this` 키워드가 필요합니다. `diet()` 내에서 `dietType`에 접근할 경우 scope가 `diet()` 안쪽으로 설정되기 때문에 reference error가 발생합니다. 따라서, `dietype` property에 접근하려면 `this` 키워드로 calling object인 `goat`를 불러와 접근해야 합니다.

​    

> **Arrow function과 `this`**
>
> ```javascript
> const goat = {
>   dietType: 'herbivore',
>   makeSound() {
>     console.log('baaa');
>   },
>   diet: () => {
>     console.log(this.dietType);
>   }
> };
>  
> goat.diet(); // Prints undefined
> ```
>
> 객체에 method를 정의할 때, arrow function 사용은 지양해야 합니다. Arrow function은 `{}`이 scope를 만들지 않기 때문입니다. 
>
> 위와 같은 경우 `this`가 가리키는 calling object는 global object입니다. `this`가 `goat`의 scope에 존재하기 때문에, `goat`를 calling하는 object인 global object가 `this`가 됩니다. 따라서, global object에는 `dietType` property가 없기 때문에, `this.dietType`은 `undefined`를 가집니다.

​    

## Privacy of object

Product를 만들다보면, 어떠한 object 내 property에 아무나 접근하지 못하게끔 막아야 하는 상황이 발생합니다. 특정 프로그래밍 언어들에서는 이러한 경우를 제어할 수 있는 privacy와 관련된 built-in 키워드를 제공합니다. 하지만 Javascript의 경우 이러한 제어 방법이 없기 때문에, 네이밍 컨벤션을 통해 다른 개발자들에게 해당 property를 어떻게 써야할 지 알려줍니다.

대표적으로 property의 식별자 앞에 `_`를 붙이는 것은 해당 property가 변형되어서는 안된다는 의미입니다.

```javascript
const robot = {
  _energyLevel: 100,
  recharge(){
    this._energyLevel += 30;
    console.log(`Recharged! Energy is currently at ${this._energyLevel}%.`)
  }
};

robot['_energyLevel'] = 'high';
robot.recharge();
// Output: Recharged! Energy is currently at high30%.
```

예를 들어, 위 코드의 경우 `_energyLevel`은 `robot['_energyLevel'] = 'high';`과 같이 실제로 변형이 가능합니다. 하지만, 개발자의 의도에 맞지 않게 string 값으로 변형함으로 인해 `high30%`와 같은 어색한 결과가 발생했습니다.

이처럼 `_`가 붙은 property는 원치않는 결과가 나올 수 있으니 직접적으로 접근하여 변형시키면 안된다는 의미를 내포합니다.

​    

## Getters & Setters

* Getters method

```javascript
const person = {
  _firstName: 'John',
  _lastName: 'Doe',
  get fullName() {
    if (this._firstName && this._lastName){
      return `${this._firstName} ${this._lastName}`;
    } else {
      return 'Missing a first name or a last name.';
    }
  }
}
 
// To call the getter method: 
person.fullName; // 'John Doe'
```

Getters는 객체 내부에서 property를 가져와 반환해주는 method입니다. Method 앞에 `get`를 사용해 구현하며, `this`를 통해 객체 내의 property를 조작합니다. Getters를 호출할 때는 마치 property에 접근하는 것 같이, `()` 없이 `.`만으로 호출합니다.

Getters를 사용하면, property에 접근할 때 원하는 action을 임의로 추가할 수 있고, 다른 개발자들이 이해하기 쉽도록 코드를 짤 수 있습니다.

* Setters method

```javascript
const person = {
  _age: 37,
  set age(newAge){
    if (typeof newAge === 'number'){
      this._age = newAge;
    } else {
      console.log('You must assign a number to age');
    }
  }
};

person.age = 40;
console.log(person._age); // Logs: 40
person.age = '40'; // Logs: You must assign a number to age
```

객체 내 property에 대한 접근을 도와주는 getters와 달리, setters는 객체 내 존재하는 property의 value를 재할당할 수 있게 도와주는 method입니다. Method 앞에 `set`을 사용해 구현하며, 마찬가지로 `this`를 사용해 객체 내 property를 조작합니다. Setters를 호출할 때도 마치 property에 값을 할당하는 것 같이 `.`만 사용하여 호출합니다.

Setters도 input checking, easier readability 등의 이점을 가집니다.

​    

> Naming of getters, setters
>
> Getters와 setters의 이름은 객체 내의 property들의 이름과 겹쳐서는 안됩니다. 만일 겹칠 경우, 끝없는 call stack error에 빠지게 됩니다. 이를 피하기 위해, property 이름 앞에 `_`를 붙여주는 것은 좋은 방법이 됩니다.

​    

## Factory function

```javascript
const monsterFactory = (name, age, energySource, catchPhrase) => {
  return { 
    name: name,
    age: age, 
    energySource: energySource,
    scare() {
      console.log(catchPhrase);
    } 
  }
};

const ghost = monsterFactory('Ghouly', 251, 'ectoplasm', 'BOO!');
ghost.scare(); // 'BOO!'
```

하나하나의 object를 직접 만드는 것은 손이 많이 가고 비효율적입니다. 따라서, 몇 가지 parameter를 받아서 customized된 object를 반환하는 함수를 만들면 다수의 object를 효율적으로 생성할 수 있습니다. 이러한 함수를 factory function이라고 합니다.

​    

## Property value shorthand

```javascript
const monsterFactory = (name, age) => {
  return { 
    name: name,
    age: age
  }
};
```

기존에는 객체에 property를 저장하기 위해 위 코드와 같이 key-value pair 방식을 사용했습니다. 다만, ES6에서는 factory function을 사용할 때와 같이 parameter의 이름과 property의 이름이 같은 경우에 대해 코드 중복을 줄일 수 있도록 property value shorthand 문법을 제공합니다.

따라서 위 코드는 다음과 같이 수정될 수 있습니다.

```javascript
const monsterFactory = (name, age) => {
  return { 
    name,
    age 
  }
};
```

​    

## Destructured assignment

객체의 key를 통해 value를 가져와 변수에 저장하던 일반적인 방식에 대해, 조금 더 간략한 destructured assignment 방식이 존재합니다.

```javascript
const vampire = {
  name: 'Dracula',
  residence: 'Transylvania',
  preferences: {
    day: 'stay inside',
    night: 'satisfy appetite'
  }
};
```

위와 같은 `vampire` 객체에서 `residence` property를 가져와 변수에 저장하고 싶다면, 두 가지 방식을 사용할 수 있습니다.

```javascript
const residence = vampire.residence; 
console.log(residence); // Prints 'Transylvania' 
```

먼저 일반적인 방식으로 key를 통해 가져올 수 있습니다.

```javascript
const { residence } = vampire; 
console.log(residence); // Prints 'Transylvania'
```

그런데 만일 key의 이름과 같은 이름으로 변수를 생성한다면, 위와 같이 `{}`를 통해 보다 간결히 property를 가져와 변수에 저장할 수 있습니다.
