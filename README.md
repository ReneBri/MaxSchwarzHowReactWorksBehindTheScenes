# Section 12: A Look Behind the Scenes of React

## My Notes

### First, Let's Look at Some JavaScript Theory Which Is Benefitial Here

## Reference vs. Primitive

**_Primitive types:_**

\_ String

\_ Number

\_ Boolean

\_ Undefined

\_ Null

\_ & ES6 Symbols

\_ Pointers(But under the hood)

Primitive values are copied by value. This means that if we do this:

```
var name = 'Rene';
console.log(name);

var secondName = name;
console.log(secondName);

name = 'Bob';
console.log(secondName);
```

Our output will be this:

```
"Rene"

"Rene"

"Rene"
```

Basically assigning name to secondName isnt a pointer, it actually makes a copy of the value and stores it in the variable.

Primitive data is stores on something called "the stack". The stack is a stack of data in your memory, which is in a place that can be accessed quickly and is actually quite limited in its storage capacity. In "the stack" when new variables are added they just get added to the top. The variable names we assign know which position in the stack they are refering to. Since the stack is limited in space that is what makes it perfect for primitive values, because they usually dont take up too much space.

If you do want to copy an objects vaues you can do either of the following: Use the Object.assign() function in JS or use the spread operator {...objectToCopy}. Object assign does not create deep copys though. It wont cory arrays inside of arrays, it will just copy the pointers to the arrays.

**_Reference types:_**

\_ Objects

\_ Arrays

\_ Functions

In order to understand this better lets write some code:

```
var person1 = {
    name: "Rene",
    age: 31,
    hobbies: ["Being a Legend", "Other stuff too"]
}
console.log(person1);

var person2 = person1;
console.log(person2);

person1.name = "Not Rene";
console.log(person2);
```

Our output this time is like this:

```
person1 {
    name: "Rene",
    age: 31,
    hobbies: ["Being a Legend", "Other stuff too"]
}

person2 {
    name: "Rene",
    age: 31,
    hobbies: ["Being a Legend", "Other stuff too"]
}

person1 {
    name: "Not Rene",
    age: 31,
    hobbies: ["Being a Legend", "Other stuff too"]
}
```

See here how changing the name of person1 affected the name of person2. This is because Objects are of reference type, which basically means that when we declare them by using another object as the value, we actually just create a pointer to that object. Now changing one actually just means we are only changing the original and since the second declared object is actually just a pointer, it will still point to the changed object, making it look like the second var changed too.

Reference types are stored on "the heap". Unlike "the stack" "the heap" takes a little bit longer to be accessed but because of that it has a much larger storage capacity and is not as "short living" as the stack, you could say. "The heap" is perfect for bigger data or data which changes frequently or is dynamic.

The heap is not managed like the stack. Data doesnt get stacked on top of eachother concurrently. It's managed somewhat randomly, finding large chunks of unused storage where these arrays and objects can fit. Since its managed "randomly" each element has its own address.

When we create a new object 2 things happen: The object is stored in the heap but also a pointer is created and that pointer is stored in the stack. So the variable name we create simply stores the pointer. So the variable doesnt know the address of the object in the heap but it does know the position of the pointer in the stack. So when you call the variable youre actually calling the stack for the pointer to give you the address of the object.

So when we make a new variable which is meant to copy the object by writing this:

```
const employee2 = employee1;
```

What we're actually doing is only copying the pointer in the stack and to the object in the heap. Hence changes to one means changes to the other.

**_ Misc Details About Comparisons _**

In js you can compare two primitive values. For example:

```
'hi' === 'hi'
```

And you will get true returned. But you cannot compare two reference values. For example:

```
{ name: 'Rene' } === { name: 'Rene' }
```

For that we would get false returned.

## Closures

JavaScript closures are functions that can access values outside of their own curly braces.

In order for the interpreter to call a function it needs to know about the function itself and any other data from the surrounding environment (AKA lexical environment) that it depends on. Everything needs to be tidied up into a box before it can be sent to the v8 engine.

Below is an example of a fully self-contained function (AKA not a closure);

```
function pureFunc(a, b) {
    return a + b;
}
```

When it's called, it gets pushed onto the callstack where it's executed and its internal data is only stored in the stack until its popped back off the call stack.

Closure example:

```
let b = 3;

function pureFunc(a, b) {
    return a + b;
}
```

But if a function accesses data from outside of its scope, like from an outer function or the global environment. That leaves us with an open expression that accesses other free variables throughout the environment. Now in order for the interpreter to call this function and to also know the value of these free variables, it creates a closure in order to store them in a place in memory where they can be accessed later. It stores them in the Heap Memory, since i'm assuming it stores them as an object and this is why functions are actually objects in js. Unlike the call stack, the heap is long term memory which decides later on when to be deleted using its own garbarge collection. So a closure is not only a function but its a function combined iwth its outer state or "lexical environment".

Closures are good for containing data and making sure not to pollute the global environment:

```
function outer (enteredName) {
    let name = enteredName;
    return function inner () {
        console.log(`Hey ${name}`);
    }
}
```

Now we could make a function factory like this

```
const hiMom = outer("Janet");
const hiDad = outer("Tony");
```

Also a cool example of things you can do here:

```
const makeSentence = (firstHalf) => {
	return (secondHalf) => {
  	return firstHalf + secondHalf;
  }
}

const addSentence = makeSentence("This is the first half... ");

console.log(addSentence("And this is the second half."));
```

## Now for React

**How does React work?**

Lets not forget that React is a JS library for building user interfaces. It is all about components, we use these components to build the user interfaces. ReactDOM is our interface to the web. React itself doesn't know anything about the web/browser. For example knows how to work with components but it doesnt care if those components contain html elements or even fictional elements. Its ReactDOM which cares about that and brings the components to the screen. React is just the library which manages components, states and component states and finds out how components need to change as compared to their previous state. React hands all that information off to the interface its working with, that being ReactDOM. ReactDOM is responsible for working with the real DOM (AKA the browser) so the user can then see it.

So React deals with the state, props and the context (component-wide data) of a component and if it sees a change that needs to be drawn to the screen, it lets ReactDOM know so that ReactDOM can do its thing.

**_But how??_**
As we said React is concerned with components and what it does is it uses the concept of 'The Virtual DOM'. It determines how the component tree currently looks and what it should look like after a state update. That information is then handed off to ReactDOM, which now knows about the differences and how it should manipulate the real DOM in order to match the virtual DOM.

Each time state changes that affects a component, that component is re-evaluated (the function re-run). BUT it is important to note that re-evaluating a component IS NOT the same as re-rendering the DOM. So just because a component function is re-executed by React does not mean the DOM is re-rendered. Instead we have to differenciate between the component part and the real DOM. The real DOM is only updated in the places based on the difference React derived between the previous state of a component and its tree and the current state after the components prop, state or context changed. So where the component and react might re-evaluate a function often, the real DOM is rarely changed and changed only when needed. Thats important for performace, because making a virtual comparison between the previous state and the current state is actually quite cheap and easy to do, it happens only in memory. Reaching out to the real DOM however, which is rendered in the browser, is pretty expensive. Its a performance intensive task. And if you make even tiny changes in a page all of the time, then your page might become slow. Thats why React has the structure of doing virtual comparisons with the virtual DOM and then only passing the changes between your last snapshot and the current snapshot to the real DOM.

This is also known as Virtual DOM diffing. For example:

Previous Evaluation Results:

```
<div>
    <h1>Hi there</h1>
</div>
```

Current Evaluation Results:

```
<div>
    <h1>Hi there<h1>
    <p>Here I am</p>
</div>
```

In this example, React would determine that the difference between these two snapshots is this paragraph element. And it would report this change to ReactDOM so that ReactDOM can update the real DOM and update this paragraph. ReactDOM would not re-render the entire DOM, it would not touch this pre-existing h1 element, it would ONLY insert the paragraph element after the h1 element inside of the div. Thats how React works behind the scenes in a nutshell.

When a function component re-renders into the actual DOM all other nested components also re-render. This is because they are returned from the function. To put it simply - The parent component re-evaluates, then so does the child component. Just because the child function is run again, does not mean the real DOM is touched. But the function will get re-evaluated.

Re-evaluating / re-executing a component function will show in the console.log EVEN if no changes to the real DOM are made.

**React.memo**

If we want to save on performance because we know that a child function component will not change there is a way to stop it from re-evaluating. To do this we go into the child component and we wrap the export in a memo, like so:

```
export default React.memo(Component);
```

This only works with functional components. And this works because memo tells React to look at the components props and only if the old state snapshot of the props is different to the new state snapshot, then re-evaluate the component. So if the parent component is re-evaluated but the React.memo() components props did not change. the re-evaluation of that perticulat component will be skipped.

The optomization method React.memo comes at a cost. This tells React that whenever the component is to be re-rendered, it has to compare the previous prop values to the current prop values. This means it has to both store the prop values and evaluate the prop values to make the comparison, which comes at its own cost. It's hard to say when this should or shouldnt be used as the amount of props a component takes and the size of a component and its children vary so much. It can be good using this on a high level of the component tree to avoid unnecessary re-evaluations of entire segments of the app.

**Functions Being Primitive**

Functions which are declared inside of a React component are re-declared each time a component is re-evaluated. This can lead to issues, especially when they are mentioned inside of a useEffect dependency array. This is because after each re-evaluation the function will be re-decalred and hence it is a new function, triggering the dependency array again in an infinite loop. Also, if you use the React.memo() to export a component and one of its props is a function it will always re-evaluate dispite having the React.memo() because it sees that the function is new. This means you have actually just made your app less optimized because the memo means there is the added storage and comparison of props as well as always re-evaluating anyways. But there is a solution. And that is...

**useCallback**

So, since functions in js are actually objects and therefore reference types, we need a way to store them so that they do not re-declare on every re-evaluation. Thankfully, React gives us a hook to deal with this: the useCallback hook. useCallback basically tells React to only re-create the pointer in the stack and not the object in the heap, so that when it compares the "two objects" it is actually just comparing both pointers which are pointing to the same object together. Basically it stores the object in Reacts internal storage and always just uses that in the re-renders rather than re-declaring the function again.

**useCallback Dependencies**

Dependencies in the dependency array should be the same as they are for useEffect. As in any outside values like state that the function depends on should be added in the array.

When we use useCallback React basically locks in all the values of the variables which we use in our function> rather than saving the variable itself, its saves the value of the variable at the time of the function creation. This can be an issue if we, for example, had a piece of state which something was conditionally defined by. So in order to have a new snapshot of the function when these important pieces of state change we add them to the dependency array.
