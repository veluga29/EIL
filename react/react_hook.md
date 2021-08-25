## Functional components

지금까지 JavaScript의 클래스를 사용해서 정의한 리액트의 component들은 함수를 사용해서 정의할 수도 있습니다. 이를 function component라고 합니다. Function component는 간단하고 직관적이라는 장점이 있습니다.

```jsx
// A component class written in the usual way:
class MyComponentClass extends React.Component {
  render() {
    return <h1>Hello world</h1>;
  }
}

// The same component class, written as a stateless functional component:
const MyComponentClass = () => {
  return <h1>Hello world</h1>;
}

// Works the same either way:
ReactDOM.render(
	<MyComponentClass />,
	document.getElementById('app')
);
```

Function component는 위와 같이 함수 형태로 작성하며, `render()` 메서드를 사용하지 않고 JSX expression을 바로 리턴하는 방식으로 작성합니다.

Function component는 `props` 역시 전달받을 수 있습니다. 

```jsx
function WelshCorgi (props) {
  return (
    <div>
      <p>{props.prompt}</p>
    </div>
  );
}
 
ReactDOM.render(
  <WelshCorgi feed="High quality dog feed" />,
  document.getElementById('app');
);
```

`props`는 parameter로 정의해 전달받고, `props.propertyName` 형식으로 접근합니다.

​    

## Hook

Hook은 function component에서 component의 state와 이후의 렌더링 관련 side effects를 관리하도록 도와주는 함수들입니다. 클래스에서는 작동되지 않지만, function component에서 lifecycle적인 특징들도 관리할 수 있도록 도와줍니다.

​    

## State hook - `useState` 

```jsx
import React, { useState } from "react";
 
function Toggle() {
  const [toggle, setToggle] = useState('off');
 
  return (
    <div>
      <p>The toggle is {toggle}</p>
      <button onClick={() => setToggle("On")}>On</button>
      <button onClick={() => setToggle("Off")}>Off</button>
    </div>
  );
}
```

`useState`는 리액트 라이브러리에서 제공하는 JavaScript 함수로, 호출 시 두 가지 value가 담긴 array를 리턴합니다.

- *current state* - the current value of this state
- *state setter* - a function that we can use to update the value of this state

State에 대한 초깃값은 `useState`에 인자로 넣어진 값으로 설정할 수 있습니다. 초깃값이 중요하지 않은 경우, 인자를 넣지 않고 초깃값을 `undefined` 상태로 두어도 상관없으나 `null` 값이라도 넘겨주는 것이 가독성을 높이는 방법이 될 수 있습니다.

`useState`를 사용해서 임의의 value를 인자로 state setter 함수를 호출하면, 현재 state를 새로운 state로 update할 수 있습니다. 특히 state setter 함수가 호출되면 리액트는 자동으로 해당 component를 다시 렌더링하므로 변경한 새로운 state value가 바로 반영됩니다.

```jsx
import React, { useState } from 'react';
 
export default function Counter() {
  const [count, setCount] = useState(0);
 
  const increment = () => setCount(prevCount => prevCount + 1);
 
  return (
    <div>
      <p>Wow, you've clicked that button: {count} times</p>
      <button onClick={increment}>Click here!</button>
    </div>
  );
}
```

만일 기존의 state를 활용해 계산한 값으로 state를 update하고 싶다면, state setter 함수에 콜백 함수를 인자로 전달하면 됩니다. 위와 같이 기존 state `count`를 활용해 `prevCount + 1` 값으로 state를 update하고 싶다면, `setCount(prevCount => prevCount + 1)`처럼 콜백 함수를 state setter 함수의 인자로 넣어줍니다. 특정한 상황에서는 `setCount(count +1)` 같이 바로 값을 update할 수도 있지만, 콜백 함수를 사용하는 방법이 모든 상황에서 더 안전하다는 점을 유의합니다.

```jsx
import React, { useState } from "react";
 
const options = ["Bell Pepper", "Sausage", "Pepperoni", "Pineapple"];
 
export default function PersonalPizza() {
  const [selected, setSelected] = useState([]);
 
  const toggleTopping = ({target}) => {
    const clickedTopping = target.value;
    setSelected((prev) => {
     // check if clicked topping is already selected
      if (prev.includes(clickedTopping)) {
        // filter the clicked topping out of state
        return prev.filter(t => t !== clickedTopping);
      } else {
        // add the clicked topping to our state
        return [clickedTopping, ...prev];
      }
    });
  };
 
  return (
    <div>
      {options.map(option => (
        <button value={option} onClick={toggleTopping} key={option}>
          {selected.includes(option) ? "Remove " : "Add "}
          {option}
        </button>
      ))}
      <p>Order a {selected.join(", ")} pizza</p>
    </div>
  );
}
```

만일 state의 값이 Array 타입인 경우, state를 update할 때 이전 state의 Array를 그대로 변경하지말고 새로운 Array로 변경 내역을 copy해서 state에 할당해야 함을 유의합니다. 위에서도 `return prev.filter(t => t !== clickedTopping);` 혹은 `return [clickedTopping, ...prev];`으로 새로운 Array를 만들어 리턴합니다.

```jsx
export default function Login() {
  const [formState, setFormState] = useState({});
 
  const handleChange = ({ target }) => {
    const { name, value } = target;
    setFormState((prev) => ({
      ...prev,
      [name]: value
    }));
  };
 
  return (
    <form>
      <input
        value={formState.firstName}
        onChange={handleChange}
        name="firstName"
        type="text"
      />
      <input
        value={formState.password}
        onChange={handleChange}
        type="password"
        name="password"
      />
    </form>
  );
}
```

State의 타입이 Object인 경우에도 update할 state 값은 변경된 내역을 새로 copy한 Object가 되어야 합니다. 또 Object를 arrow function에서 return할 때는 `{}`가 겹치는 문제가 발생할 수 있기 때문에, 반환할 Object를 `()`로 감싸줄 필요가 있습니다.

​    

## Separate Hooks for Separate States

```jsx
function Subject() {
  const [state, setState] = useState({
    currentGrade: 'B',
    classmates: ['Hasan', 'Sam', 'Emma'],
    classDetails: {topic: 'Math', teacher: 'Ms. Barry', room: 201};
    exams: [{unit: 1, score: 91}, {unit: 2, score: 88}]);
  });
```

State와 같은 dynamic data를 다루기 위해서는 state 변수마다 각각 hook을 지정해 관리하는 것이 편합니다. 위와 같이 하나의 복잡한 Object를 state로 하여 하나의 hook으로 관리한다면, 복잡한 state들을 각각 copy할 때 매우 불편해집니다.

```jsx
function Subject() {
  const [currentGrade, setGrade] = useState('B');
  const [classmates, setClassmates] = useState(['Hasan', 'Sam', 'Emma']);
  const [classDetails, setClassDetails] = useState({topic: 'Math', teacher: 'Ms. Barry', room: 201});
  const [exams, setExams] = useState([{unit: 1, score: 91}, {unit: 2, score: 88}]);
  // ...
}
```

따라서, 위와 같이 state 변수마다 hook을 만들어 관리한다면 훨씬 간단하고 쉽게 state를 관리할 수 있습니다.

​    

## Effect hook

Effect hook은 렌더링 이후의 side effects를 관리하는 함수입니다. fetch API를 통해 백엔드로부터 데이터를 받아오거나 DOM을 읽고 변화를 주는 등의 side effect를 발생시키는 작업들을 관리하며, 보통 다음 3가지 상황에서 사용합니다.

1. Component가 DOM에 mount되어 렌더링될 때
2. State 혹은 props가 변화하여 component가 다시 렌더링 될 때
3. Component가 DOM에서 unmount되어 렌더링될 때

​    

## Effect hook - `useEffect`

```jsx
import React, { useState, useEffect } from 'react';
 
function PageTitle() {
  const [name, setName] = useState('');
 
  useEffect(() => {
    document.title = `Hi, ${name}`;
  });
 
  return (
    <div>
      <p>Use the input field below to rename this page!</p>
      <input onChange={({target}) => setName(target.value)} value={name} type='text' />
    </div>
  );
}
```

`useEffect`는 component를 렌더링할 때마다 다른 함수를 호출하기 위해 사용합니다. 이로 인해, `useEffect`는 첫 번째 인자로 렌더링 후 호출할 목적의 콜백 함수를 받습니다. 그리고 이러한 콜백 함수를 effect라고도 부릅니다. 예를 들어, 위 코드에서는 `() => { document.title = name; }`가 effect입니다.

Effect는 현재 state에도 접근할 수 있습니다. 다만 component 렌더링이 일어난 다음 DOM이 update되면 그 후 effect가 호출되므로, state도 update가 완료된 상태에서 접근하게 됩니다.

​    

##  Clean Up Effects

어떠한 effect들은 메모리 누수를 피하기 위하여 항상 제거하는 작업을 동반해주어야 합니다. 예를 들어, effect를 사용해 직접 DOM 내의 element에 event listener를 추가하는 경우, 원하는 작업이 끝나면 해당 event listener를 반드시 다시 제거해주어야 합니다. 그렇지 않으면 렌더링될 때마다 호출되는 effect hook의 특성으로 인해, 이후 발생하는 수많은 렌더링 상황마다 event listener가 의도치 않게 끊임없이 추가되어 메모리가 터지는 상황이 생길 수 있습니다. 따라서 다음과 같이 `useEffect`의 effect 내에서 event listener를 제거하는 함수를 반환하여, 추가했던 event listener를 제거해줍니다.

```jsx
useEffect(()=>{
  document.addEventListener('keydown', handleKeyPress);
  return () => {
    document.removeEventListener('keydown', handleKeyPress);
  };
})
```

Effect가 반환하는 함수는 `useEffect`가 항상 clean up 함수로 간주하므로, 리액트는 effect 작업이 끝나면 자동적으로 이를 호출합니다.

​    

## Dependency array

Effect는 기본적으로 매 렌더링이 일어나는 상황마다 호출됩니다. 그러나 dependency array를 사용하면, effect를 원하는 때에만 호출하도록 설정할 수 있습니다. Dependency array는 `useEffect`의 두 번째 인자로 넣는 array를 말합니다.

만일 component가 mount되어 첫 번째 렌더링을 할 때만 effect hook을 호출하고 최종 렌더링에서 clean up하고 싶다면, 빈 array `[]`를 `useEffect()`의 두 번째 인자로 넣어줍니다.

반면에, dependency array에 특정 변수를 요소로 넣는다면, 해당 변수의 값이 변할 때만 effect가 호출됩니다.

```jsx
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // Only re-run the effect if the value stored by count changes
```

​    

## Hook을 사용하는 규칙

더욱 복잡한 React 앱에서 혼란을 피하기 위해, hook은 다음과 같은 규칙을 지키며 사용합시다.

1. Hook을 항상 top level에서만 사용합시다.

   리액트는 function component 내에서 정의한 순서에 따라 hook과 함께 관리되는 data와 function들을 인식합니다. 따라서, conditions, loops, nested functions 안에서 hook을 사용하지 말아야 합니다.

   ```jsx
   if (userName !== '') {
     useEffect(() => {
       localStorage.setItem('savedUserName', userName);
     });
   }
   ```

   조건문을 쓰고 싶다면 위와 같이 쓰지 말고, 다음과 같이 effect 내에서 사용해 동일한 결과를 얻을 수 있습니다.

   ```jsx
   useEffect(() => {
     if (userName !== '') {
       localStorage.setItem('savedUserName', userName);
     }
   });
   ```

2. Hook은 react function component 내에서만 사용합시다.

   Function component이외에 hook을 사용할 수 있는 곳은 custom hook을 제외하고 존재하지 않습니다. Class component나 일반적인 JavaScript 함수 내에서 hook을 사용하지 맙시다.

​    

## Separate Hooks for Separate States

```jsx
// Handle menuItems with one useEffect hook.
const [menuItems, setMenuItems] = useState(null);
useEffect(() => {
  get('/menu').then((response) => setMenuItems(response.data));
}, []);
 
// Handle position with a separate useEffect hook.
const [position, setPosition] = useState({ x: 0, y: 0 });
useEffect(() => {
  const handleMove = (event) =>
    setPosition({ x: event.clientX, y: event.clientY });
  window.addEventListener('mousemove', handleMove);
  return () => window.removeEventListener('mousemove', handleMove);
}, []);
```

Effect hook 역시 모든 로직을 한 곳에 모아두면 가독성이 떨어지고 복잡해집니다. 따라서 위와 같이 effect 마다 따로 hook을 만드는 것을 지향합니다. 

​    

## Reference

[Learn React - Codecademy](https://www.codecademy.com/courses/react-101)

