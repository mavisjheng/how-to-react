## Props and State

**[Props](https://www.w3schools.com/REACT/react_props.asp)** is short for properties and they are used to pass data between React components. Props are passed to components via HTML attributes and they're **read-only, component cannot change the props**.

The important part of props is that data with **props are being passed in a uni-directional flow, one way from parent to child**.

```javascript
class Car extends React.Component {
  render() {
    return <h2>I am {this.props.brand}!</h2>;
  }
}

class Garage extends React.Component {
  render() {
    return (
      <div>
        <h1>Who lives in my garage?</h1>
        <Car brand="Ford" />
      </div>
    );
  }
}
```

React has another special built-in object called **[state](https://www.w3schools.com/REACT/react_state.asp)**, which allows components to create and manage their own data. So unlike props, components cannot pass data with state, but they can create and manage it **internally**.

State should not be modified directly, but it can be modified with a special method called **setState()**. The state of one component will often become the props of a child component.

```javascript
class App extends React.Component {
  constructor() {
    super();
    this.state = {
      age: 3,
      name: "Mary",
    };
  }

  increase = () => {
    this.setState({ age: this.state.age + 1 });
  };

  render() {
    return (
      <div>
        <p>{`${this.state.name} is ${this.state.age} years old`}</p>
        <button onClick={this.increase}>Plus 1</button>
      </div>
    );
  }
}
```

```javascript
// wrong
this.state.id = "2020";

// correct
this.setState({
  id: "2020",
});
```

**State** is the local state of the component which cannot be accessed and modified outside of the component. It's similar to variables within a function.

**Props** get passed to the component, **make components reusable** by giving components the ability to receive data from their parent component in the form of props. They are similar to function parameters.

Props and State are both plain JavaScript objects. **An update can be caused by changes to props or state.**

## Component

**[Components](https://reactjs.org/docs/components-and-props.html)** let you split the UI into independent, reusable pieces, and think about each piece in isolation. Conceptually, components are like JavaScript functions. They accept arbitrary inputs (called **props**) and return React elements describing what should appear on the screen.

**Always start component names with a capital letter.** React treats components starting with lowercase letters as DOM tags. For example, `<div />` represents an HTML div tag, but `<Welcome />` represents a component and requires Welcome to be in scope.

```javascript
// React elements that represent DOM tags
const element = <div />;
```

```javascript
// user-defined components, must start with a capital letter
const element = <Welcome name="Sara" />;
```

```javascript
// Rendering components
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

const element = <Welcome name="Sara" />;

ReactDOM.render(element, document.getElementById("root"));
```

### Make a React component reusable

Component can be tricky to change because of all the nesting, and it is also hard to reuse individual parts of it.

```javascript
// data is hard-coded, can't be reusable
const Avatar = () => {
  return <img src="/static/images/avatar/1.jpg" alt="Cindy Baker" />;
};
```

In order to make component reusable, we have to extract it to its own component.

Extracting components might seem like grunt work at first, but having a palette of reusable components pays off in larger apps. A good rule of thumb is that if a part of your UI is **used several times** (Button, Panel, Avatar), or is **complex enough on its own** (App, FeedStory, Comment), it is a good candidate to be extracted to a separate component.

Make a component more reusable by turning it **from specific to more generic**. Naming props **from the component’s own point of view rather than the context in which it is being used**.

```javascript
// before extracting components, all the nesting and hard to reuse individual parts of it
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img
          className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">{props.author.name}</div>
      </div>
      <div className="Comment-text">{props.text}</div>
      <div className="Comment-date">{formatDate(props.date)}</div>
    </div>
  );
}
```

```javascript
// extracting components: Avatar
// The Avatar doesn’t need to know that it is being rendered inside another component
// This is why we have given its prop a more generic name: user rather than author
function Avatar(props) {
  return (
    <img className="Avatar" src={props.user.avatarUrl} alt={props.user.name} />
  );
}
```

```javascript
// extracting components: UserInfo
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">{props.user.name}</div>
    </div>
  );
}
```

```javascript
// after extracting components
function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">{props.text}</div>
      <div className="Comment-date">{formatDate(props.date)}</div>
    </div>
  );
}
```

☞☞☞☞ [How To Make A React Component Reusable?
](https://www.robinwieruch.de/react-reusable-components)

## Function component and Class component

### Function Component

- [Function components](https://www.robinwieruch.de/react-function-component) are literally JavaScript functions, they are a simpler way to write components that **only contain a render method and don’t have their own state**. They are typically arrow functions but can also be created with the regular function keyword.

- Props are the function component's parameters. Whereas the component can stay generic, we decide from the outside what it should render or how it should behave. Also called Dumb Components, Presentational Components or Stateless Components as **they simply accept data and display them in some form, their only responsibility is to present something to the DOM**.

- Since props are always coming as object, and most often you need to extract the information from the props anyway, JavaScript [object destructuring](https://pjchender.blogspot.com/2017/01/es6-object-destructuring.html) comes in handy

```javascript
// Function Component / Arrow Function Component
function Headline() {
  const greeting = "Hello Function Component!";
  return <h1>{greeting}</h1>;
}

const Welcome = (props) => {
  return <h1>Hello, {props.name}</h1>;
};

const Welcome = ({ name }) => {
  return <h1>Hello, {name}</h1>;
};

const Welcome = (props) => <h1>Hello, {props.name}</h1>;
```

### Class Component

- To define a React component class, you need to **extend React.Component**. The only method you must define in a class component is `render()`, and the return can only return one parent element.

- Also called Smart Components or Stateful Components as they tend to implement logic and they can maintain its own state.

- Class Components are more complex than function components including **constructors, lifecycle methods, render() function and state management**.

```javascript
// Class Component
import React, { Component } from "react";

class Welcome extends Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

// Class Component
import React from "react";

class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

## Lifecycle

Each component has several [lifecycle methods](https://reactjs.org/docs/react-component.html#the-component-lifecycle) that you can override to run code at particular times in the process.

Commonly used lifecycle methods:

- Mounting – Birth of component, when an instance of a component is being created and inserted into the DOM

  - constructor()
  - render()
  - componentDidMount()

- Updating – Growth of component, re-rendered, an update can be caused by changes to props or state

  - render()
  - componentDidUpdate()

- Unmounting – Death of component, when a component is being removed from the DOM
  - componentWillUnmount()

![Lifecycle](./images/lifecycle.png)

### Mounting: constructor ➝ render ➝ componentDidMount

### constructor

The first thing that gets called inside class components is constructor, **it does not apply to function components**. The constructor in a React component is called **before the component is mounted**.

The main purpose for writing constructor is to initialize state in a class, create [refs](https://reactjs.org/docs/refs-and-the-dom.html) and [bind event handlers](https://reactjs.org/docs/handling-events.html) to the component instance using .bind().

```javascript
// with Constructor
// super refers to the parent class constructor (In our example, it points to the React.Component implementation)
// importantly, you can’t use this in a constructor until after you’ve called the parent constructor
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = { color: "red", count: 0 };
    this.inputRef = React.createRef();
    this.handleOnPress = this.handleOnPress.bind(this);
  }

  handleOnPress() {
    this.setState({ count: this.state.count + 1 });
  }

  render() {
    return (
      <div>
        <h2>
          I have {this.state.count} {this.state.color} Car
        </h2>
        <button onClick={this.handleOnPress}>Click</button>
        <input ref={this.inputRef} />
      </div>
    );
  }
}
```

Note that using a constructor is optional if your Babel setup has support for [class properties](https://babeljs.io/docs/en/babel-plugin-transform-class-properties/), by using class properties you can **initialize local state without using the constructor and declare class methods by using arrow functions without the extra need to bind them**. Class properties are exactly that, properties defined on the class.

```javascript
// without Constructor
// using class properties: babel-plugin-transform-class-properties
class App extends React.Component {
  state = { color: "red", count: 0 };
  inputRef = React.createRef();

  handleOnPress = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    return (
      <div>
        <h2>
          I have {this.state.count} {this.state.color} Car
        </h2>
        <button onClick={this.handleOnPress}>Click</button>
        <input ref={this.inputRef} />
      </div>
    );
  }
}
```

### render

As the name suggests it handles the component rendering to the UI. It happens during the mounting and updating of component.

The render() function should be pure with no side-effects and will always return the same output when the same inputs are passed, this means that you can not setState() within a render().

When called, it should examine this.props and this.state and return one of the following types:

- **React elements.** Typically created via JSX. For example, `<div />` and `<MyComponent />` are React elements that instruct React to render a DOM node, or another user-defined component.

- **Arrays and fragments.** Let you return multiple elements from render.

  ```javascript
  // A common pattern in React is for a component to return multiple elements.
  // Fragments let you group a list of children without adding extra nodes to the DOM.
  render() {
    return (
      <React.Fragment>
        <ChildA />
        <ChildB />
        <ChildC />
      </React.Fragment>
    );
  }
  ```

  ```javascript
  // There is a new, shorter syntax you can use for declaring fragments. It looks like empty tags
  render() {
    return (
      <>
        <ChildA />
        <ChildB />
        <ChildC />
      </>
    );
  }
  ```

- [**Portals.**](https://reactjs.org/docs/portals.html) Let you render children into a different DOM subtree. Portals provide a first-class way to render children into a DOM node that exists outside the DOM hierarchy of the parent component.

  ```javascript
  // The first argument (child) is any renderable React child, such as an element, string, or fragment.
  // The second argument (container) is a DOM element.
  ReactDOM.createPortal(child, container);
  ```

- **String and numbers.** These are rendered as text nodes in the DOM.

- **Booleans or null.** Render nothing. (Mostly exists to support `return test && <Child />` pattern, where test is boolean.)

### componentDidMount()

Now your component has been mounted and ready, that’s when the next React lifecycle method componentDidMount() comes in play. componentDidMount() is invoked immediately after a component is mounted (inserted into the tree). **If you need to load data from a remote endpoint, this is a good place to instantiate the network request**.

Most common use case for componentDidMount: starting API calls to load in data for your component or all the setup you can't do without a DOM.

```javascript
import React, { Component } from "react";

class App extends Component {
  state = {
    data: [],
  };

  // Code is invoked after the component is inserted into the DOM tree
  componentDidMount() {
    const url =
      "https://en.wikipedia.org/w/api.php?action=opensearch&search=Seona+Dancing&format=json&origin=*";

    fetch(url)
      .then((result) => result.json())
      .then((result) => {
        this.setState({
          data: result,
        });
      });
  }

  render() {
    const { data } = this.state;

    const result = data.map((entry, index) => {
      return <li key={index}>{entry}</li>;
    });

    return <ul>{result}</ul>;
  }
}

export default App;
```

### Updating: render ➝ componentDidUpdate

### componentDidUpdate()

```javascript
componentDidUpdate(prevProps, prevState, snapshot);
```

This lifecycle method is invoked as soon as the updating happens. The most common use case for the componentDidUpdate() method is **updating the DOM in response to prop or state changes**. This is also a good place to do network requests as long as you compare the current props to previous props.

You can call setState() in this lifecycle but note that **it must be wrapped in a condition to check for state or prop changes from previous state or you’ll cause an infinite loop**.

```javascript
// check if there has been a change in props from what it currently is
// in this case, there won’t be a need to make the API call if the props did not change.
componentDidUpdate(prevProps) {
  // typical usage, don't forget to compare the props or state!!
  if (this.props.userID !== prevProps.userID) {
    this.fetchData(this.props.userID);
  }
}
```

### Unmounting: componentWillUnmount

### componentWillUnmount()

componentWillUnmount() is invoked immediately before a component is unmounted and destroyed. **Perform any necessary cleanup in this method**, such as invalidating timers, canceling network requests, or cleaning up any subscriptions that were created in componentDidMount().

```javascript
componentWillUnmount() {
  window.removeEventListener('resize', this.resizeListener)
  clearInterval(this.interval);
}
```

- References:
  - https://codeburst.io/use-class-properties-to-clean-up-your-classes-and-react-components-93185879f688
  - https://babeljs.io/docs/en/babel-plugin-transform-class-properties/
  - https://programmingwithmosh.com/javascript/react-lifecycle-methods/
  - https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/
  - https://www.taniarascia.com/getting-started-with-react/
  - [React DevTools for Chrome](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
