## Component of React

Component란 하나의 작업을 수행하는 재사용할 수 있는 작은 코드 뭉치를 의미합니다. 여기서 하나의 작업이란 대체로 HTML 코드를 렌더링하는 것을 말합니다.

​    

## Necessary import

Component를 사용하기 위해서는 `React` 객체를 import 해두어야 합니다. `React` 객체에는 리액트 라이브러리를 사용하기 위한 필수적인 메서드들이 담겨있습니다. JSX expression을 사용하는데도 `React` 객체가 반드시 필요하므로, 첫 줄은 항상 다음 코드로 시작하도록 합니다!

```jsx
import React from 'react';
```

또한, component 사용을 위해 `ReactDOM` 객체도 import합니다. `ReactDOM` 객체는 `React` 객체와 마찬가지로 React와 관련된 메서드들을 가지고 있습니다. 그러나 `React`에는 순수하게 React만을 위한 메서드가 담겨있는 반면, `ReactDOM`은 React와 DOM의 상호작용을 돕는 메서드들이 담겨 있다는 차이점이 있습니다. 따라서, 다음 코드 역시 함께 사용합니다.

```jsx
import ReactDOM from 'react-dom';
```

​    

## 클래스를 활용한 Component 생성

리액트 component는 자바스크립트의 클래스 혹은 함수를 통해 생성할 수 있습니다. 여기서는 클래스 component에 초점을 맞추겠습니다.

클래스 component는 리액트 라이브러리의 `Component` 클래스를 상속받아서 정의합니다. 클래스를 사용하면 원하는 만큼 인스턴스로 component를 만들어 렌더링할 수 있다는 이점이 생깁니다.

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
 
class MyComponentClass extends React.Component {
  render() {
    return <h1>Hello component</h1>;
  }
}

ReactDOM.render(
  <MyComponentClass />,
  document.getElementById('app')
);
```

위와 같이 `React.Component`를 상속받으면 새로운 component 클래스를 만들어 customizing할 수 있습니다. 여기서 `React.Component`는 `React` 객체의 property이며, `Component`는 클래스입니다. 

여기서 또 하나 유의할 점은 새로 정의한 component 클래스 body에는 반드시 `render()` 메서드를, `render()` 메서드 내에는 주로 JSX expression을 반환하는 `return` statement를 정의해야 한다는 부분입니다. 해당 클래스에는 어떤 component를 만들 것인지 instruction을 제시해줘야 하기 때문에, 이를 위한 `render()` 메서드와 `return` statement를 필수적으로 정의합니다.

그리고 이렇게 만들어진 클래스를 활용해 component를 자유롭게 생성할 수 있습니다. 앞서 JSX element를 사용했듯이, 클래스의 이름을 사용해 `<MyComponentClass />` 코드를 쓰면 component 인스턴스가 생성됩니다!

이렇게 생성한 component 인스턴스를 `ReactDOM.render()`에 인자로 던져주면, 해당 component를 화면에 렌더링할 수 있습니다. Component는 클래스에서 정의한 `render()` 메서드를 가지고 있기 때문에, `ReactDOM.render()`는 인자로 받은 component의 `render()` 메서드를 자동으로 호출하게끔 하여 JSX expression을 반환받고 화면에 렌더링합니다.

​    

> **Class component의 naming convention**
>
> 새로 정의한 클래스 component의 이름은 첫 글자부터 대문자를 사용하는 UpperCamelCase를 따릅니다. 이것은 Java의 naming convention에서 차용되었으며, 원래의 JavaScript 클래스를 만들 때도 마찬가지의 convention을 따릅니다.
>
> UpperCamelCase를 사용하는 또 다른 이유는 리액트 자체적으로도 찾을 수 있습니다. JSX element는 HTML-like인 경우와 component인 경우로 나뉩니다. 이 때, UpperCamelCase로 쓰인 JSX element가 있다면, 해당 element가 component instance임을 쉽게 파악할 수 있습니다.
>
> ex) ShinyBrownHairOfWelshCorgi
>
> ex) \<WelshCorgiLegComponent />

​    

## render() 메서드에 정의할 수 있는 것

```jsx
class Random extends React.Component {
  render() {
    // First, some logic that must happen
    // before rendering:
    const n = Math.floor(Math.random() * 10 + 1);
    // Next, a return statement
    // using that logic:
    return <h1>The number of Welsh Corgi is {n}!</h1>;
  }
}
```

Component의 `render()` 메서드에는 항상 `return` statement가 와야 합니다. 다만 이에 더하여, 렌더링 직전의 간단한 계산 역시 둘 수 있는 위치입니다.

```jsx
class Random extends React.Component {
  // This should be in the render function:
  const n = Math.floor(Math.random() * 10 + 1);
 
  render() {
    return <h1>The number of Welsh Corgi is {n}!</h1>;
  }
};
```

그러나 위와 같이 `render()` 메서드 바깥에 변수를 정의하는 것은 syntax error를 유발하니, 메서드 안쪽에서 정의할 것을 유의해야 합니다.

​    

## Event listener in a component

```jsx
class MyClass extends React.Component {
  myFunc() {
    alert('Stop it. Stop hovering my Welsh Corgi.');
  }
 
  render() {
    return (
      <div onHover={this.myFunc}>
      </div>
    );
  }
}
```

위와 같이 component 클래스의 메서드로 정의한 event handler 함수를 사용하여, event listener를 component에 정의할 수 있습니다. Event listener 속성에 `this`를 사용해 메서드를 부여하는 것으로 적용 가능합니다.

​    

## Reference

[Learn React - Codecademy](https://www.codecademy.com/courses/react-101)