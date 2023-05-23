# Class Components

**What & Why**

They are an alternative to functional components. Defining them like so:

```
import {Component} from "react";

class Product extends Component {
    render() {
        return <h2>{this.props.name}</h2>
    }
}
```

Components can be defined as JS calsses where a render() method defines the to-be-rendered output.

FUNCTIONAL COMPONENTS ARE THE MOST MODERN AND DEFAULT WAY TO DECALRE A COMPONENT. THIS IS LEGACY.

Prior to React 16.8 you had to use class-based components to deal with state and effects. You could not handle state or side effects in functional components before 16.8 but after that Hooks were introduced.

Class-based components cant use hooks.

Inside of the components we use the render method. React knows to call this when a component is found inside of the jsx < Component /> for example. The render method is the return equivilant.

In class-based components we do not receive the props argument like we do in the functional components. Instead we have to import Component from 'react' and then extend the component class with our own class and by doing that we get access to the 'this' property where we can access the props.

**State**

In class-based components your state is ALWAYS an object. You set your state by declatinf it inside of a constructor function inside of the class.

```
class Users extends Component {
    constructor() {
            this.state = {};
        }
}
```

The property must also be called state.... This is not up to us. We must also group all of our state into this one object with each field and value being a seperate piece of state.... Which might be quite convenient?

To change the state we use a special method which is provided on the default Component class called this.setState(); this.setState also always takes an object. This object does not override the old state, instead it merges with the old state, so if we have multiple state properties and only define one in the setState function, we will still have all peices of state but the state we re-defined will be updated. These properties in the objects also support updating functions with the prevState argument.

```
class Users extends Component {
    toggleState() {
        this.setState(curState => {
            return { showUsers: !curState.showUsers };
        )}
    }
}
```

Here's a full example of a class based component using what I've talked about above:

```
class Users extends Component {
	constructor() {
		super();
		this.state = {
			showUsers: true,
		};
	}
	toggleUsersHandler() {
		this.setState();
	}

	render() {
		const usersList = (
			<ul>
				{DUMMY_USERS.map((user) => (
					<User key={user.id} name={user.name} />
				))}
			</ul>
		);

		return (
			<div className={classes.users}>
				<button onClick={this.toggleUsersHandler.bind(this)}>
					{this.state.showUsers ? "Hide" : "Show"} Users
				</button>
				{this.state.showUsers && usersList}
			</div>
		);
	}
}

export default Users;
```
