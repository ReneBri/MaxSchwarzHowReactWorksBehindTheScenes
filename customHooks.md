## What are custom hooks?

Custom hooks are just re-usable functions which contain stateful logic.

Unlike regular functions, custom hooks can use other React hooks and React state.

Custom hooks must always start with 'use'.

For each call of the custom hook new state is created. It is only logic this is re-used each call.

## Forms and User Input

** Whats so complex about forms? **

Forms and inputs can assume a broad variety of states. Valid and invalid for example.

Times you can validate:

-   when a form is submitted
    -   allows the user to enter a valid value before warning them
    -   avoid unnecessary warnings but maybe present reedback too late
-   when an input loses focus
    -   allows the user to enter a valid value before warning them
    -   very useful for untouched forms
-   on every keystroke
    -   warns a user before they have a chance to enter valid values
    -   if applied only on invalid inputs, has the potential of providing more direct feedback

Validate 'onBlur" is when the input loses focus.

** A custom hook for user input & validation **

```
import {useState} from "react";

const useInput = (validateValue) => {
	const [enteredValue, setEnteredValue] = useState("");
	const [isTouched, setIsTouched] = useState(false);

	const valueIsValid = validateValue(enteredValue);
	const hasError = !valueIsValid && isTouched;

	const inputValueChangeHandler = (e) => {
		setEnteredValue(e.target.value);
	};

	const inputBlurHandler = (e) => {
		setIsTouched(true);
	};

	const resetInput = () => {
		setEnteredValue("");
		setIsTouched(false);
	};

	return {
		value: enteredValue,
		isValid: valueIsValid,
		hasError,
		inputValueChangeHandler,
		inputBlurHandler,
		resetInput,
	};
};

export default useInput;
```

Then to impliment it in the form page we do this:

```
const {
    value: enteredName,
    isValid: enteredNameIsValid,
    hasError: nameInputIsInvalid,
    inputValueChangeHandler: nameInputChangeHandler,
    inputBlurHandler: nameInputBlurHandler,
    resetInput: resetNameInput,
} = useInput((value) => value.trim() !== "");

const {
    value: enteredEmail,
    isValid: enteredEmailIsValid,
    hasError: emailInputIsInvalid,
    inputValueChangeHandler: emailInputChangeHandler,
    inputBlurHandler: emailInputBlurHandler,
    resetInput: resetEmailInput,
} = useInput((value) => value.trim().includes("@") && value.trim() !== "");
```
