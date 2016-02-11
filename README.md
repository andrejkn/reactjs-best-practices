# ReactJS the good parts

## Pros of ReactJS
#### Awesome featrures that come out of the box
- DOM tree abstraction
- Declarative programming
- Componentization
- JSX - clear

## Cons
#### Mutative objects (JavaScript inherent issue)

Consider the following example:

```js
const Session = {
  userName: 'Bob',
  token: '1l23asdf923f92fifa93',
};

Session.userName = 'Jane'; // Why is this bad?
```

Another bad React example:

```js
class Counter extends React.Component {
  state = {
    count: 0
  };

  _increment = () => {
    // overwriting the original state
    this.state = {
      count: this.state.count + 1,
      action: 'incremented'
    }

    this.setState(this.state);
  }

  _decrement = () => {
    // overwrite a state property
    this.state.count = this.state.count - 1;

    // augment the state object
    this.state.action = 'decremented';

    // actually setting the state and calling react to
    //   re-render
    this.setState(this.state); // NO!!!!
  }
  render() {
    return (
      <div>
        <span>{`Count ${ this.state.cont }`}</span>
        <span>{`Count was ${ this.state.action }`}</span>
        <button onClick={ this._increment } />
      </div>
    );
  }
}
```

- The purpose of this example is to show how things can go wrong in React if not being careful.

- In the example above, we can easily overwrite or augment the `state` object. This is legal in JavaScript since objects are **NOT** *immutable*.

- The issue is that we modify the `state` object and loose the original value before we even get to notify React that the state has changed by calling `setState()`.

###### fix/avoid-direct-state-mutation

```js
class Counter extends React.Component {
  state = {
    count: 0,
    action: ''
  };

  _increment = () => {
    // create newState object
    const newState = {
      count: this.state.count + 1,
      action: 'incremented'
    }

    this.setState(newState);
  }

  _decrement = () => {
    // better than overwriting the original state.count
    const newCount = this.state.count - 1;

    // augment the state object
    const newAction = 'decremented';

    // update the original state with the new values
    //   and trigger re-render
    this.setState({
      count: newCount,
      action: newAction,
    });
  }

  render() {
    return (
      <div>
        <span>{`Count ${ this.state.cont }`}</span>
        <span>{`Count was ${ this.state.action }`}</span>
        <button onClick={ this._increment } />
      </div>
    );
  }
}
```

###### fix/prevent-state-mutation
```js
class Counter extends React.Component {
  state = ImmutableObj({
    count: 0,
    action: ''
  });
```

- The immutable object would provide methods that can be used to create a copy
of the old object with newly specified values.

- Consider an immutable object:
```js
const person = ImmutableObj({
  name: 'Robert',
  age: 43,
  profession: 'PHP developer',
  job: 'currently unemployed'
});
```

- The immutable object would have methods such as:

```js
  person.get('name') // returns 'Robert'

  // returns a new object with the name Json
  const newPerson = person.set('name', 'Json');
  // person is not changed
  // newPerson === {
  //   name: 'Json',
  //   age: 43,
  //   profession: 'PHP developer',
  //   job: 'currently unemployed'
  // }
```

```js
  person.merge(otherObjectLiteral OR otherImmutableObject);
```

```js
  person.update((prev) => {
  return prev.set('key', 'value');
  });
```

```js
  ...
  _increment = () => {
    const newState = this.state
      .set('count', this.state.count + 1) // return new copy of state
                                          //  with a new count value
      .set('action', 'incremented'); // return new copy of the previous copy
                                     // of state with a action set to 'incremented'

    this.setState(newState);
  }
  ...
```

OR

```js
  ...
  _increment = () => {
    const newState = this.state.merge({
      count: this.state.count + 1,
      action: 'incremented'
    });

    this.setState();
  }
  ...
```

```js
  ...
  render() {
    return (
      <div>
        <span>{`Count ${ this.state.get('cont') }`}</span>
        <span>{`Count was ${ this.state.get('action') }`}</span>
        <button onClick={ this._increment } />
      </div>
    );
  }
}
```

### De-centalized state

```js
class LoginForm extends React.Component {
  state = {
    username: '',
  }

  _handleUserEntry = (e) => {
    this.setState({
      username: e.target.value;
    });
  }

  render() {
    return (
      <div>
        <input
          onKeyUp={ this._handleUserEntry }
          type="text"
          placeholder="Enter Username"
        />
        <button onClick={
          this.props.submitLogin(this.props.username)
        } />
      </div>
    );
  }
}
```


Solutions:
- use Immutable
- use Redux
