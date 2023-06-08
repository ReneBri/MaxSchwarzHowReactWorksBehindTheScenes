# Redux

** What is Redux? **

Redux is a state management system for cross component or app-wide state.

The three different types of state are:

1. Local State
2. Cross-Component
3. App-Wide State

**Local State**
Is state that belongs to a single component. For eample if we have a user input and we use state to store whatever the user enters inn a local variable. Or if we have a button that toggles a details field. This should usually be managed with either useState or useReducer.

**Cross-Component State**
Is that that affects multiple components. For example if we have a button that opens or closes a modal overlay. The trigger for the modal per definition is not inside of the modal but outside of it and to close it you trigger something inside fo the modal. This is cross-component state. We also implement that with useState or useReducer but with this we need to pass props with prop chains or prop drilling.

**App-Wide State**
Is state that affects most/all components of an application. An example here is user authentication. Wer can also manage this with useState or useReducer and then pass data around using props. But the better way is to use React Context. But en even better way (sometimes) is to use Redux!

**Why do we need Redux if we already have React Context?**
Redux is a state-wide management system for cross-component or app-wide state. And so is React Context.

Well, React Context has a couple of _potential_ disadvantages. And its only potential because if these disadvantages are not applicable to your app, then you don't need Redux.

What are these disadvantages?

-   Complex Setup / Management - this definitly depends on the scale of app you are building. If it gets too big you can ended up with your < app /> nested within X amount of context providers.
-   Performance - We have an official post from someone from the React core team stating that the new Context (2018) is ready to be used in low frequency updates (user auth, updating a theme, for example). He states for high frequency changes that it isnt that good for those. And he said it isnt ready to replace things like "Flux-like state propergation". Redux is a "Flux-like" state management library.

## How Redux works

**Core Concepts**
Redux is about having ONE central data (state) store in your application. And thats important - ONE STORE TO RULE THEM ALL. In this store you would store user auth, themeing, maybe some user input state you want to save, as long as it is cross-component or app-wide. Ultimatly we have data in this store so that we can access it within or components.

For this data accessing our components set up "subscriptions" to your store. And whenever our data changes the store notifies components, then components can get the data they need, for example the user auth "slice" of the Redux store, then they can use it. This is how we get the data out of the store... But how about changing in the store?

One rule is that components themselves NEVER, I REPEAT NEVER directly manipulate the store data. So we have that description, but we don't have that data flow in the other direction. At least it isnt a direct data flow. Instead for that we use a concept called reducers, or a reducer function. The reducer function we set up is responsible for mutating (or changing) the store data. One important thing to note is that the reducer function we use here is NOT the useReducer hook - Reducer functions are a general CS concept. Reducer functions are functions that take some input and then transform (/ reduce) that input into a new result.

Okay so we have have the components subscribing to the data and we have a reducer function which will transform the state, but how do get the component to tell the reducer what to reduce? Another concept is an "action" and we get components to "dispatch" actions. Therefor we could also say that components "trigger" certain actions. Now an action is really just a simple JS object that describes the type of operation the Reducer should perform. Almost like giving an order in the military. Think of for example, Order 66 in Star Wars: The order has been given beforehand, so the troops know what to do, then all the sith had to do was to send out a signal saying "Complete order 66". After this the reducer then spits out a new state that will replace the existing state in the app and pass that throught he subscription to the components.

**createStore()**
Your IDE will tell you it's depreciated - It's still useable though. It is better to use the Redux toolkit but as far as learning fundamentals goes, we need to learn this. So let's crack on.

**More Core Redux Concepts**
The Redux Function is a standard function from the Redux Lib and it will ALWAYS receive 2 parameters - Old / Existing State & Dispatch Action. And ti always outputs a new state object.

A reducer function here should be a "pure function". Being that the same input always leads to the same output and there should be no side-effects inside of the function. So you should send any http requests or write/fetch something to local storage etc etc. A reducer should just be something that takes the inputs, then produces the expected output.... A new state object.

Theoretically this state could just be a number or a single string, but in reality we use this because of the state complexity, which usually means its an object.

Here's an interesting way of explaining Redux within Node:

```
const redux = require("redux");

// our reducer function which we pass to the createStore()
const counterReducer = (state = {counter: 0}, action) => {
	if (action.type === "INCREMENT") {
		return {
			counter: state.counter + 1,
		};
	} else if (action.type === "DECREMENT") {
		return {
			counter: state.counter - 1,
		};
	} else {
		return state;
	}
};

// our newly created store
const store = redux.createStore(counterReducer);

// declaring our function which we want to have subscribed to our store
const counterSubscriber = () => {
	const latestState = store.getState();
	console.log(latestState);
};

// subscribe is a method on the store which we pass our subscribed function in order for it
// to be re-triggered on every store update. A sort of two way binding.
// so both the reducer and the subscriber function will be executed by Redux.
store.subscribe(counterSubscriber);

setInterval(() => {
	store.dispatch({type: "DECREMENT"});
}, 1000);
```

As you can see, its very similar to useReducer.

**Installing React Redux**
As we've seen above Redux can be used in a lot of difference JS environments. Because of this, off the bat Redux doesn't know about React to change this we have to install react-redux along with redux itself.

```
npm i redux react-redux
```
