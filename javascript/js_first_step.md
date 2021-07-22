# First step of Javascript

## 출력

`console` 객체의 `log` 메서드를 사용해 콘솔에 출력합니다.

* `console.log("print out something");`

​    

## 주석 처리

* Single line comment

  `// something comment`

* Multi-line comment

  `/* something comment */`

​    

## Fundamental data types

- *Number*: Any number, including numbers with decimals: `4`, `8`, `1516`, `23.42`.
- *String*: Any grouping of characters on your keyboard (letters, numbers, spaces, symbols, etc.) surrounded by single quotes: `' ... '` or double quotes `" ... "`. Though we prefer **single quotes**. Some people like to think of string as a fancy word for text.
- *Boolean*: This data type only has two possible values— either `true` or `false` (without quotes). It’s helpful to think of booleans as on and off switches or as the answers to a “yes” or “no” question.
- *Null*: This data type represents the intentional absence of a value, and is represented by the keyword `null` (without quotes).
- *Undefined*: This data type is denoted by the keyword `undefined` (without quotes). It also represents the absence of a value though it has a different use than `null`.
- *Symbol*: A newer feature to the language, symbols are unique identifiers, useful in more complex coding. No need to worry about these for now.
- *Object*: Collections of related data.

*Object*를 제외한 나머지 6개의 data types는 **Primitive data type**이라고 부릅니다.

​    

## Operator

Javascript에는 다음과 같은 **산술 연산자**들이 존재합니다.

* Add: `+` (복합 대입 연산자는 `+=`)
* Minus: `-` (복합 대입 연산자는 `-=`)
* Multiply: `*` (복합 대입 연산자는 `*=`)
* Divide: `/` (복합 대입 연산자는 `/=`)
* Modulo: `%` (복합 대입 연산자는 `%=`)
* Increment operator: `++` (+1을 함과 동시에 할당까지 진행)
* Decrement operator: `--` (-1을 함과 동시에 할당까지 진행)

**비교 연산자**는 `===`를 제외하고는 다른 언어들과 비슷한 양상을 보입니다. 비교 대상은 Number 뿐만 아니라 String도 포함됩니다.

- Less than: `<`
- Greater than: `>`
- Less than or equal to: `<=`
- Greater than or equal to: `>=`
- Is equal to: `===`
- Is not equal to: `!==`

**논리 연산자**는 다음과 같이 사용합니다.

- And: `&&`
- Or: `||`
- Not: `!`

​    

## 변수 (Variable)

Javascript에서는 camel case가 변수명 convention으로 사용됩니다. 

> favoriteFood, numOfSlices, etc...

또한, 변수는 값을 꼭 할당할 필요 없이 선언만 할 수도 있습니다. 이렇게 선언만 한 경우, 해당 변수에는 자동적으로 `undefined` 값이 정의됩니다.

```javascript
let price;
console.log(price); // Output: undefined
price = 350;
console.log(price); // Output: 350
```

Javascript에서는 변수를 선언하는 키워드의 종류로 `var`, `let`, `const`가 있습니다.

* `var`: 새로운 변수를 생성할 수 있게 해주는 기본 키워드입니다. 2015년 등장한 ES6 버전 이전에 가장 많이 쓰였습니다.

* `let`: 변수에 다른 값이 재할당될 수 있음을 의미하는 키워드입니다. ES6 버전에서 처음 등장했습니다.

  ```javascript
  let dog = 'Welsh Corgi';
  console.log(dog); // Output: Welsh Corgi
  dog = 'Poodle';
  console.log(dog); // Output: Poodle
  ```

* `const`: 변수에 다른 값이 재할당될 수 없음을 의마하는 키워드입니다. 실제로 다른 값을 재할당하면 `TypeError`가 발생합니다. 또한, 변수는 선언함과 동시에 값이 할당되어야 합니다. 선언만 할 경우 `SyntaxError`가 발생합니다. `let`과 마찬가지로 ES6 버전에서 처음 등장했습니다.

​    

## String concatenation

Javascript에서도 `+`를 사용해 string 간의 concatenation을 수행할 수 있습니다. 

```javascript
const favoriteAnimal = 'Welsh Corgi';
console.log('My favorite animal: ' + favoriteAnimal);  // My favorite animal: Welsh Corgi
```

만일 data type이 String이 아닌 data와 concatenation을 할 경우, String type으로 auto converting 되어 정상적으로 concatenation됩니다.

```javascript
const count = 3;
console.log('There are ' + count + ' Welsh Corgies!');  // There are 3 Welsh Corgies!
```

​    

## String interpolation

ES6 버전에서는 template literal을 사용해 변수를 string에 삽입하는 interpolation을 수행할 수 있습니다. Interpolation은 `` `를 사용해 표현하며, placeholder `${변수명}`를 사용해 변수를 삽입합니다. 이렇게 만든 template literal은 문자열로서 취급됩니다.

```javascript
const myPet = 'Welsh Corgi';
console.log(`I own a pet ${myPet}.`);  // I own a pet Welsh Corgi.
```

Interpolation은 코드의 가독성을 높이므로, 만들어질 string의 모습을 누구나 쉽게 알 수 있다는 장점이 있습니다.

​    

## Conditional statement

다음은 Javascript에 존재하는 몇 가지 조건문들의 문법입니다.

* Syntax of `If` statement 

  ```javascript
  if (condtion) {
    codeblock
  } else if (condition) {
    codeblock
  } else if (condition) {
    codeblock
  } else {
    codeblock
  }
  ```

* Syntax of ternary operator

  ```javascript
  (condition) ? (codeblock when true) : (codeblock when false);
  ```

* Syntax example of `switch` statement

  ```javascript
  let dog = 'Welsh Corgi';
   
  switch (dog) {
    case 'Golden Retriever':
      console.log('Golden Retriever, bow wow!');
      break;
    case 'Dachshund':
      console.log('Dachshund, bow wow!');
      break;
    case 'Welsh Corgi':
      console.log('Welsh Corgi, bow wow!');
      break;
    default:
      console.log('No correct dog but bow wow!');
      break;
  }
  ```

​    

## Reference

[Codecademy - introduction to javascript](https://www.codecademy.com/courses/introduction-to-javascript/)