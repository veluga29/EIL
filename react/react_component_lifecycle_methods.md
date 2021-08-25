# Component lifecycle methods

리액트의 수많은 component들은 각각 자신의 lifecycle을 가집니다. 보통 component의 lifecycle 다음과 같이 구성됩니다.

1. *Mounting*, when the component is being initialized and put into the DOM for the first time
2. *Updating*, when the component updates as a result of changed state or changed props
3. *Unmounting*, when the component is being removed from the DOM

그리고 이러한 lifecycle 각각을 제어하기 위해 개발자들이 사용할 수 있는 lifecycle method들이 존재합니다. 대표적으로 `constructor()`와 `render()` 역시 lifecycle method에 해당됩니다! `constructor()`는 mounting phase에 첫 번째로 호출되는 메서드로, `render()`는 mounting과 updating phase에 자주 등장하는 메서드로 분류할 수 있습니다.

​    

## `componentDidMount()`

`componentDidMount()` 메서드는 mounting phase에서 마지막으로 호출되는 메서드입니다. Mounting phase 안에서 메서드들은 다음과 같은 순서로 호출됩니다.

1. The constructor
2. `render()`
3. `componentDidMount()`

`componentDidMount()`를 활용하면 1초씩 현 시각을 계속 알려주는 시계를 만들 수 있습니다.

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };
  }
  render() {
    return <div>{this.state.date.toLocaleTimeString()}</div>;
  }
  componentDidMount() {
    // Paste your code here.
    const oneSecond = 1000;
    setInterval(() => {
      this.setState({ date: new Date() });
    }, oneSecond);
  }
}

ReactDOM.render(<Clock />, document.getElementById('app'));
```

​    

## `componentWillUnmount`

```jsx
import React from 'react';

export class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };
  }
  render() {
    return <div>{this.state.date.toLocaleTimeString()}</div>;
  }
  componentDidMount() {
    const oneSecond = 1000;
    this.intervalID = setInterval(() => {
      this.setState({ date: new Date() });
    }, oneSecond);
  }
  componentWillUnmount() {
    clearInterval(this.intervalID);
  }
}
```

`componentWillUnmount` 메서드는 unmounting phase에서 사용됩니다. Component가 완전히 없어지기 전에 호출되기 때문에, side-effect를 발생시키는 불필요한 비동기 함수를 종료하기 적합한 시기입니다. 위와 같이 시간을 지속적으로 업데이트하는 시계의 `setInterval()` 함수를 멈추려면, `componentWillUnmount()` 메서드에서 `clearInterval()`을 사용합니다. `intervalID`를 `clearInterval()`의 인자로 전달해주면 해당 `setInterval()` 함수를 종료시킵니다.

​    

## `componentDidUpdate`

Updating phase에서 주로 사용하는 메서드는 `render()`, `componentDidUpdate`입니다. Update는 props와 state의 변화가 일어날 때 발생하는 작업으로, update 관련한 로직은 `componentDidUpdate`에서 사용하는 것이 유용합니다.

​    

## Reference

[Learn React - Codecademy](https://www.codecademy.com/courses/react-101)

[this interactive diagram](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

