## Programming patterns

리액트는 자주 사용되는 프로그래밍 패턴이 존재합니다.

1. Scene 1 - Stateful components to stateless components

   Stateful component가 자신의 state setter 함수를 `props`로 child component에 전달하면, child component의 어떠한 event에 의해 해당 함수가 호출되어 parent component의 state를 변경합니다. 그리고 parent component는 변경된 state를 `props`로 또 다른 child component(=sibling component)에게 전달해 해당 child component에서 화면에 표시합니다.

2. Scene 2 - Separating container components from presentational components

   State를 가지거나 calculation 등의 functional part를 담당하는 component는 container component로, 렌더링을 담당하는 component는 presentational component로 분리해야 합니다. 분리된 presentational component는 항상 container component에 의해서 렌더링되어야 합니다.



## Style Name Syntax

일반적인 JavaScript에서 style의 name은 hyphenated-lowercase로 이루어져 있습니다.

```jsx
const styles = {
  'margin-top': '20px',
  'background-color': 'green'
};
```

반면에, 리액트는 style name이 camelCase로 이루어져 있습니다.

```jsx
const styles = {
  marginTop: '20px',
  backgroundColor: 'green'
};
```

​    

## Style Value Syntax

일반적인 JavaScript에서는 `"450px"`, `"20%"` 처럼 숫자와 단위를 함께 적어 string 형태로 style value를 사용해야 합니다. 하지만, 리액트에서는 `px`에 한해서 생략이 가능하고, 이 경우 숫자도 string이 아닌 number 그대로 사용하는 것이 가능합니다. 물론 기존의 string 형태도 그대로 사용 가능합니다.

```jsx
{ fontSize: 30 }
```

다만, 다른 단위를 사용하고 싶을 때는 기존의 string 형태로 사용합니다.

```jsx
{ fontSize: "2em" }
```

​    

## `propTypes`

`propTypes`는 리액트에서 자주 사용되는 특징입니다. Prop이 전달될 것이 예상되는 component에 올바른 prop이 전달되었는지에 대한 validation을 도와주고, documentation을 통해 component의 상황을 한눈에 파악할 수 있도록 도와줍니다.

```jsx
import PropTypes from 'prop-types';
```

`propTypes`를 사용하기 위해선 `'prop-types'` 라이브러리를 import해야 합니다. 

```jsx
import React from 'react';
import PropTypes from 'prop-types';

export class MessageDisplayer extends React.Component {
  render() {
    return <h1>{this.props.message}</h1>;
  }
}

// This propTypes object should have
// one property for each expected prop:
MessageDisplayer.propTypes = {
  message: PropTypes.string
};
```

그리고 미리 정의된 component에 위와 같이 property를 추가하는 방식으로 `propTypes`를 정의할 수 있습니다. 이 때, `propTypes`의 value는 object 형태여야 함을 유의합니다. 그리고 해당 object의 각각의 property는 component에 전달될 것이 기대되는 prop의 이름으로 설정합니다.

```jsx
Runner.propTypes = {
  message:   PropTypes.string.isRequired,
  style:     PropTypes.object.isRequired,
  isMetric:  PropTypes.bool.isRequired,
  miles:     PropTypes.number.isRequired,
  milesToKM: PropTypes.func.isRequired,
  races:     PropTypes.array.isRequired
};
```

`PropTypes`를 통해 설정할 수 있는 data type의 이름은 위와 같습니다. `isRequired`의 경우, prop이 잘 전달되는지 확인해서 만일 잘 전달되지 않으면 console에 warning을 띄어주는 역할을 합니다.

```jsx
const Example = (props) => {
  return <h1>{props.message}</h1>;
}
 
Example.propTypes = {
  message: PropTypes.string.isRequired
};
```

만일 function component에 `propTypes`를 추가하고 싶다면, 위와 같이 function component 자체의 property로 `propTypes`를 지정합니다.

​    

## React forms

```jsx
import React from 'react';
import ReactDOM from 'react-dom';

export class Input extends React.Component {
  constructor(props) {
    super(props);
    this.state = { userInput: '' };

    this.handleUserInput = this.handleUserInput.bind(this);
  }

  handleUserInput(e) {
    this.setState({userInput: e.target.value});
  }

  render() {
    return (
      <div>
        <input type="text" value={this.state.userInput} onChange={this.handleUserInput} />
        <h1>{this.state.userInput}</h1>
      </div>
    );
  }
}

ReactDOM.render(
	<Input />,
	document.getElementById('app')
);
```

일반적인 form은 유저가 input field에 계속 타이핑하더라도 submit 버튼을 누르기전까지는 서버에서 그 사실을 알지 못합니다. 즉, submit 이전까지 프론트가 알고 있는 input 정보와 서버가 알고 있는 input 정보 사이에 불일치가 존재합니다.

그러나 이러한 불일치는 웹사이트의 third part에서 해당 정보를 필요로 할 때, 프론트냐 서버냐에 따라 다른 결과를 내어 문제가 발생할 수 있습니다. 이를 해결하기 위해, 리액트 form은 모든 new character와 deletion에 대한 프론트 및 서버의 동기화를 지원하여 application의 모든 요소가 일관성 있게 동작하도록 합니다. 특히, 일반적인 `<form>` tag를 굳이 사용하지 않고 위 코드처럼 `<input>` tag만으로 이를 구현할 수 있습니다.

​    

## Uncontrolled vs Controlled component

Uncontrolled component란 스스로 state를 가지고 그 값을 기억하는 component를 말합니다. 반면에 controlled component는 스스로 state를 가지지 않고 다른 component에 의해 관리되어지는 component를 말합니다. 

리액트에는 주로 controlled component가 많고 이러한 component는 스스로에 대한 정보를 `props`를 통해 얻게 됩니다.

​    

## Reference

[Learn React - Codecademy](https://www.codecademy.com/courses/react-101)
