
Layered architecture is a principle that applies in many areas of software development and specifically when writing code for others to use. The desire to provide a set of building blocks that do not force a developer to fully opt in or opt out of your tools increases the likelihood of long term adoption. This makes your code more [[Reusable vs Useful|Useful]]. Furthermore, a layered architecture makes your code more easily extendable in the future and therefore simpler to maintain with less risk of painting yourself into a corner.


## What is it?
Long story short, layered architecture is the idea that having multiple layers of abstraction will result in the above benefits. To do so, you must separate what differs from what stays the same with an eye towards flexibility. A reusable component is no good if a future requirement means having to rebuild it. 

## In Practice
![[Pasted image 20230822120302.png]]
We have a basic input that wraps the html input element.

![[Pasted image 20230822120325.png]]
We have a form component that renders it's children, keeps track of various state values, and provides functions to set and reset the state (via the useContext hook).
![[Pasted image 20230822120403.png]]
So how do we get these two elements to communicate with each other? 

The most basic answer is to simply wrap whichever inputs need form access in a single component that is in turn wrapped by the provider. We then just connect the relevant form functions to the inputs.
##### Parent Component
```jsx
return (<Form initialData={{foo: "", bar: ""}}) >
	<ExampleForm />
</Form>
```

##### ExampleForm Component
```jsx
const {set, formState, validation} = useForm()
return (
	<Input value={formState["foo"]} 
	onChange={(e) => set("foo", e.target.value)} 
	errorFlag={validation["foo"]}
	/>
	<Input value={formState["bar"]} 
	onChange={(e) => set("bar", e.target.value)} 
	errorFlag={validation["bar"]}
	/>
)
```

This works great but creating an entirely separate component file for non-functional reasons (to simply get at the useForm hook) is not great. Additionally, you can imagine that following this pattern would result in a ton of mostly-repeated code for each and every input. Applications often have dozens of forms with dozens of inputs each so this is no good.

Hopefully you have seen some of "what stays the same" in the above example. If you have, then you may try to do something similar to the following.

#### FormInput
```jsx
const FormInput = ({path, onChange, errorFlag}) => {
	const {set, formState, validation} = useForm()

	<Input value={formState[path]} 
	onChange={(e) => {
		set(path, e.target.value)
		onChange(e)
	}} 
	errorFlag={validation[path]}
	/>
}
```

##### Parent Component
```jsx
return (<Form initialData={{foo: "", bar: ""}}) >
	<FormInput path="foo" />
	<FormInput path="bar" />
</Form>
```

Because the "path" of the input's bound value was the only part of the code that changed from input to input, we abstracted that away and simplify the integration of our `Input` component and our `Form` component.

This is all well and good. But what if we need to bind a form value to another type of input? Maybe a `select`? We could follow the same pattern as above.

```jsx
const FormSelect = ({path, onChange, errorFlag, options}) => {
	const {set, formState, validation} = useForm()

	<Input value={formState[path]} 
	onChange={(e) => {
		set(path, e.target.value)
		onChange(e)
	}} 
	errorFlag={validation[path]}
	/>
}
```
### References:
https://surma.dev/things/cost-of-convenience/ 
https://jesseduffield.com/React-Abstractions/