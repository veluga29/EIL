## React basic

React.js는 Facebook 엔지니어들이 개발한 UI 개발 목적의 JavaScript 라이브러리입니다. 리액트의 컴포넌트 기반 개발은 Single Page Application을 비롯한 프론트 개발에 큰 변화를 이끌었으며, 근 5~6년간 자바스크립트 생태계의 가장 중요한 존재 중 하나로 자리해 왔습니다. 최근에는 더 효율적인 프론트 개발 라이브러리들이 많이 등장했지만, 리액트의 영향력은 여전히 직간접적으로 느껴집니다.

​    

## JSX

```jsx
const h1 = <h1>Welsh Corgi!!</h1>;
```

JSX는 리액트에 사용되기 위해 쓰여진 JavaScript의 syntax extension입니다. 보통 JavaScript 파일 속에 JavaScript 코드와 HTML 코드들이 혼용되어 쓰여진 것들로 통용되므로, JSX 코드에는 HTML같은 코드가 포함되지만 실제로 HTML은 아닙니다.

특히, JSX는 웹 브라우저가 바로 읽을 수 없습니다. 그러므로 JSX가 포함된 JavaScript 파일을 통상적으로 사용하려면, JSX compiler를 통해 일반적인 JavaScript 코드로 컴파일해야 합니다.

​    

## JSX element

```jsx
<h1>Hello world</h1>
```

또한, JavaScript 파일 속에 HTML과 똑같이 생긴 위와 같은 코드들을 JSX element라고 부릅니다. JSX element는 JavaScript 코드로 간주되어, 변수에 저장되거나 함수의 인자로 입력되는 등 일반적인 모든 프로그래밍에 문제 없이 사용됩니다.

```jsx
const welshCorgi = <img src='images/welsh.jpg' alt='welsh corgi' width='600px' height='600px' />;
```

JSX element에는 HTML 때와 마찬가지로 attribute 역시 적용할 수 있습니다.

```jsx
const welshCorgi = (
  <a href="https://www.shinybrownhair.com">
    <h1>
      Bow wow!
    </h1>
  </a>
);
```

Nested한 형태도 기존 HTML처럼 사용할 수 있습니다. 다만, multi-line이 될 경우 `()`로 감싸주어야 오류 없이 프로그래밍할 수 있음을 유의합시다.

```jsx
const dogs = (
  <p>I am a Poodle.</p> 
  <p>I am a Welsh Corgi. Nice to meet you!</p>
);
```

다만, JSX expression은 하나의 같은 element 단위가 되어야 하기 때문에, 위와 같이 두 개의 element를 한 번에 사용하는 것은 불가능합니다. 만일 위와 같이 쓰고 싶다면, 위 코드를 하나의 `<div></div>` 태그로 감싸서 코드가 올바르게 동작하도록 만드는 방법을 권장합니다.

​    

## Rendering

렌더링(Rendering)이란 코드를 해석해서 화면에 띄우는 작업을 의미합니다. 렌더링은 보통 리액트와 관련된 메서드들을 모아둔 `ReactDom` 라이브러리의 `ReactDOM.render()` 메서드를 사용해 진행합니다.

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(<h1>Hello world</h1>, document.getElementById('app'));
```

`ReactDOM.render()` 메서드에는 첫 번째 인자로 화면에 띄울 JSX expression을 사용합니다. 그리고 두 번째 인자로 해당 JSX expression을 띄울 container가 될 HTML 태그를 찾아 넘깁니다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="utf-8">
	<link rel="stylesheet" href="/styles.css">
	<title>Learn ReactJS</title>
</head>

<body>
  <main id="app"></main>
</body>

</html>
```

예를 들어 위와 같은 `index.html` 문서가 있다면, `<main id="app"></main>` 태그 속에 첫 번째 인자로 넘긴 JSX expression이 위치해 화면에 렌더링됩니다.

​    

## Virtual DOM

```jsx
const dog = <h1>Welsh Corgi</h1>;
 
// This will add "Welsh Corgi" to the screen:
ReactDOM.render(dog, document.getElementById('app'));
 
// This won't do anything at all:
ReactDOM.render(dog, document.getElementById('app'));
```

`ReactDOM.render()`의 장점은 변경이 있는 DOM elements만 update한다는 점입니다. 수많은 DOM elements가 있을 때, 변경된 것들만 update하는 것은 React의 큰 이점입니다. React는 virtual DOM을 통해 이를 실현합니다.

Virtual DOM이란 리액트에서 실제 DOM object와 대응되는 가벼운 카피 버전의 가상 DOM object를 말합니다. Virtual DOM은 실제 DOM과 같은 property들을 가지지만, DOM의 변화를 화면에 직접 띄우는 기능은 없기 때문에, 일반 DOM 조작보다 빠르다는 장점이 있습니다.

따라서, 리액트는 다음과 같은 방식으로 DOM을 update합니다.

1. 전체 virtual DOM을 업데이트합니다.
2. Update한 virtual DOM과 이전 virtual DOM의 snapshot을 비교하여 변화된 부분들을 확인합니다.
3. 변화된 부분만 실제 DOM object에서 update합니다.
4. 실제 DOM의 변화가 화면에 반영됩니다.

​    

> DOM manipulation의 단점
>
> 과거 일반적인 자바스크립트 라이브러리들은 DOM manipulation을 할 때, DOM element 하나가 변경되면 모든 element들을 다시 update해야 해서 비효율적이었습니다. 덕분에 DOM이 커질수록 cost가 더욱 늘어났는데, 리액트의 virtual DOM 도입은 cost 문제를 혁신적으로 해결했습니다. 변경된 특정 DOM element만 update하는 virtual DOM의 특징이 DOM manipulation 속도를 혁신적으로 향상 됐습니다.

​    

## Advanced syntax of JSX

JSX의 문법은 대게 HTML과 동일하지만 미묘하게 다른 부분들이 존재하므로 유의해야 합니다.

### className

```jsx
<h1 className="dog">Welsh Corgi</h1>
```

HTML에서 사용되는 `class` 속성은 JSX에서 `className`으로 사용합니다. 이는 JavaScript가 `class`를 예약어로 갖고 있어서 JSX를 JavaScript로 변압할 때 키워드가 겹치는 문제가 발생하기 때문입니다. 대신 `className`은 JSX가 렌더링될 때, `class` 속성으로서 자동으로 인식됩니다.

### self-closing tag

```html
Fine in HTML with a slash:
 
  <br />
 
Also fine, without the slash:
 
  <br>
```

HTML에서는 `<img>` 태그나 `<input>` 태그 같은 요소들의 끝 부분 `>` 앞에 `/`를 쓰는 것이 선택적입니다. 

```jsx
Fine in JSX:
 
  <br />
 
NOT FINE AT ALL in JSX:
 
  <br>
```

하지만, JSX에서는 self-closing tag에 `/`를 반드시 써줘야 합니다. (그렇지 않으면, 에러가 발생합니다.)

### JavaScript in JSX in JavaScript

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

ReactDOM.render(
  <h1>{2 + 3}</h1>,
  document.getElementById('app')
);
// Output on monitor: 5
```

JSX expression 안에 일반적인 JavaScript 코드를 사용하고 싶다면, `{}`를 사용합니다. `{}` 안에 위치한 코드들은 JSX expression 안쪽이라도 JavaScript 코드로 인식됩니다. 여기서 `{}`는 JSX나 JavaScript가 아니라, JavaScript injection into JSX의 시작과 끝을 나타내는 marker입니다.

### Event Listener

```jsx
function myFunc() {
  alert('Welsh Corgi!!!!');
}
 
<img onClick={myFunc} />
```

JSX에서도 HTML과 같이 event listener를 사용할 수 있습니다. `on`을 접두어로 하는 속성들을 사용하면 event listener를 적용할 수 있는데, 해당 속성들의 값은 반드시 함수가 되어야 합니다.

또한, HTML에서 event listener의 이름들은 모두 소문자로 쓰이지만, JSX에서는 camelCase로 사용해야 합니다.

### Conditional statement

JSX에는 `if` 구문을 삽입할 수 없습니다. 하지만, 이를 해결할 몇 가지 방법도 존재합니다.

```jsx
const sound = 'Bow wow!';
if (sound === 'Bow wow!') {
  message = (
    <h1>
      Hey, good dog!
    </h1>
  );
} else {
  message = (
    <h1>
      I like a lot of animal!
    </h1>
  );
}
```

먼저, JSX 바깥에서 `if`를 사용해 원하는 조건문을 만들 수 있습니다.

```jsx
const sound = 'Bow wow!';
const message = (
  <h1>
    { sound === 'Bow wow!' ? 'Hey, good dog!' : 'I like a lot of animal!' }
  </h1>
);
```

혹은 삼항연산자(ternary operator)를 사용하면 JSX 내부에서도 조건문을 사용할 수 있습니다. React에서는 상당히 자주 사용되는 방법입니다.

```jsx
const tasty = (
  <ul>
    <li>Dog feed</li>
    { !puppy && <li>Dog gum</li> }
    { age > 1 && <li>bone</li> }
    { age > 5 && <li>Dog ade</li> }
    { age > 7 && <li>Dog cookie</li> }
  </ul>
);
```

만일 어떤 조건에서만 action을 취하고 다른 때는 아무 것도 하지 않는 경우라면, `&&` 연산자를 활용하는 것도 적합합니다. 즉, `&&` 연산자의 왼쪽 expression이 true일 경우에만, `&&` 연산자의 오른쪽 expression이 렌더링될 것입니다. 이러한 형태의 조건문도 React에서 자주 쓰이는 방식입니다.

### map()

```jsx
const dogs = ['Welsh Corgi', 'Poodle', 'Dachshund'];
 
const listDogs = dogs.map(dog => <li>{dog}</li>);
 
<ul>{listDogs}</ul>
```

만일 JSX element의 array를 만들고 싶다면, `.map()`을 사용하는 것이 유용합니다. React에서 자주 사용되는 방식이므로 기억해두면 좋습니다.

```jsx
// This is fine in JSX, not in an explicit array:
 
<ul>
  <li>dog 1</li>
  <li>dog 2</li>
  <li>dog 3</li>
</ul>
 
// This is also fine!
 
const liArray = [
  <li>dog 1</li>, 
  <li>dog 2</li>, 
  <li>dog 3</li>
];
 
<ul>{liArray}</ul>
```

또한, `<li>` JSX element들이 담긴 array는 위의 `{liArray}` 같이 곧바로 `<ul>`과 함께 사용하는 것이 가능합니다.

### key 속성

```jsx
<ul>
  <li key="li-01">Dog 1</li>
  <li key="li-02">Dog 2</li>
  <li key="li-03">Dog 3</li>
</ul>
```

`<li>` 태그들은 때때로 `key` 속성을 필요로 할 때가 있습니다. 특정 상황에서 `key`를 설정해두지 않으면 잘못된 순서로 list-item들이 나타날 수 있으므로, 다음과 같은 상황에서는 `key` 속성을 설정합니다. 

1. 각각의 list-item이 memory를 가질 경우 (to-do list와 같이 항목의 체크 여부를 기억해야 할 때)
2. list-item이 섞일 가능성이 있을 때

`key` 속성을 설정할 때, `key` 속성의 값은 unique해야 합니다.

### React.createElement()

React 코드를 JSX expression을 쓰지 않고도 사용할 수 있는 방법이 있습니다. 

```jsx
const h1 = <h1>Welsh Corgi</h1>;
```

위의 JSX expression으로 표현하던 기존의 코드는 다음과 같이 새로 쓰일 수 있습니다.

```jsx
const h1 = React.createElement(
  "h1",
  null,
  "Welsh Corgi"
);
```

`React.createElement()`을 사용하면 JSX expression을 쓰지 않고도 같은 기능을 하는 React 코드를 만들 수 있습니다.

사실 JSX element가 컴파일 될 때, 컴파일러는 내부적으로 해당 JSX element를 `React.createElement()` 메서드로 변형하여 호출합니다. 즉, JSX expression을 사용하기 전에는 항상 `import React from 'react';`로 `React` 객체를 import해야 하는데, 그 이유는 내부적으로 항상 `React.createElement()` 메서드가 사용 가능해야 하기 때문입니다.

​    

## Reference

[Learn React - Codecademy](https://www.codecademy.com/courses/react-101)

[Event Listener List - React.js](https://reactjs.org/docs/events.html#supported-events)

