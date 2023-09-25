Conditional rendering applies to almost any front end framework. At its base level, the concept pertains to hiding or showing an element or elements based on some logic.

Let’s say you have a `PackingList` component rendering several `Item`s, which can be marked as packed or not:

```jsx
function Item({ name, isPacked }) {
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Packing List</h1>
      <ul>
        <Item 
          isPacked={true} 
          name="Space suit" 
        />
        <Item 
          isPacked={true} 
          name="Helmet with a golden leaf" 
        />
        <Item 
          isPacked={false} 
          name="Photo of Tam" 
        />
      </ul>
    </section>
  );
}
```

Notice that some of the `Item` components have their `isPacked` prop set to `true` instead of `false`. Imagine now that you want to add a checkmark (✔) to packed items if `isPacked={true}`.

You can write this as an [`if`/`else` statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else)  in the `Item` like so:

```jsx
if (isPacked) {  

	return <li className="item">{name} ✔</li>;  

}  

return <li className="item">{name}</li>;
```

If the `isPacked` prop is `true`, this code **returns a different JSX tree.** With this change, some of the items get a checkmark at the end.

You could also do this with [[Template Literals]] and the [[Ternary Operator]].

```jsx
return <li className="item">{`${name} ${isPacked ? ✔ : ""}`}</li>;
```

### Conditionally returning nothing with `null` [](https://react.dev/learn/conditional-rendering#conditionally-returning-nothing-with-null "Link for this heading")

In some situations, you won’t want to render anything at all. For example, say you don’t want to show packed items at all. A component must return something; even if that is nothing. In this case, you can return `null`:

```js
//inside the map function to render items
if (data.isPacked) {  return null }

return <Item name={data.name}/>;
```

If `isPacked` is true, the component will return nothing, `null`. Otherwise, it will return JSX to render.

In practice, returning `null` from a component isn’t common because it might surprise a developer trying to render it. More often, you would conditionally include or exclude the component in the parent component’s JSX.

```jsx
if (packingList[i].isPacked) {  

	return null;  

}  

return <Item name={packingList[i].name} />;
```

### Logical AND operator (`&&`) [](https://react.dev/learn/conditional-rendering#logical-and-operator- "Link for this heading")

Another common shortcut you’ll encounter is the [JavaScript logical AND (`&&`) operator.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND#:~:text=The%20logical%20AND%20(%20%26%26%20)%20operator,it%20returns%20a%20Boolean%20value.) Inside React components, it often comes up when you want to render some JSX when the condition is true, **or render nothing otherwise.** With `&&`, you could conditionally render the checkmark only if `isPacked` is `true`:

```jsx
return (  

<li className="item">  

	{name} {isPacked && '✔'}  

</li>  

);
```

```js
a || b
c && d
```

This pattern is less preferred, though more readable, because of the fact that javascript will evaluate the left hand side of the "and" operator as a boolean. This means if a number is used, and that number happens to be 0, the condition will fail.

This is also prone to issues in the above example because if `isPacked` is false, we could see the string "false" in the final jsx.

### Conditionally assigning JSX to a variable [](https://react.dev/learn/conditional-rendering#conditionally-assigning-jsx-to-a-variable "Link for Conditionally assigning JSX to a variable")

When the shortcuts get in the way of writing plain code, try using an `if` statement and a variable. You can reassign variables defined with [`let`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let), so start by providing the default content you want to display, the name:

```js
let itemContent = name;
```

Use an `if` statement to reassign a JSX expression to `itemContent` if `isPacked` is `true`:
```js
if (isPacked) {  

	itemContent = name + " ✔";  

}
```

Embed the variable with curly braces in the returned JSX tree, nesting the previously calculated expression inside of JSX:
```jsx
<li className="item">  

	{itemContent}  

</li>
```

## Advanced Techniques
### Switch Statement 
The patterns above regarding if statements can also be expanded out using switch statements.

```jsx
return ( 
<div> 
	{(
		() => {
			switch(param) {
				case 'foo': return 'bar';
				default: return 'foo'; 
			} 
		} 
})()
} </div>
```

Note carefully though that you always have to use default for the switch case operator since in React, a component always needs to return an element or null.

To make it cleaner, we can get the switch out of the render in a function and just call it passing the params we want. 

```jsx
const renderSwitch = (param) => {
	switch(param) {
		case 'foo': return 'bar';
		default: return 'foo'; 
	} 
} 
return ( <div> {renderSwitch(param)} <div> ); }
```
### Enums / Lookup objects
In JavaScript, an object can be used as an [[Enum]] when it is used as a map of key-value pairs.

```js
const ENUMOBJECT = { a: '1', b: '2', c: '3', };
```

We can include a component as the value in this type of object 
```jsx
const ENUM_STATES = {
foo: <Foo />,
bar: <Bar />,
default: <Default />
};

// or, to avoid pre-rendering the JSX
const ENUM_STATES = {
foo: Foo,
bar: Bar,
default: Default
};

Object.freeze(cloneDeep(ENUM_STATES))
```

From here we can either render directly from `ENUM_STATES` or we can create a component to handle any additional rendering and / or logic.

```jsx
// render the result directly 
const MyEnumVariable = ENUM_STATES["default"]
return ( 
	<div> 
		<h1>Conditional Rendering with enums</h1> 
		{ENUM_STATES["default"]} 
		{ENUM_STATES["bar"]} 
		{ENUM_STATES["foo"]} 
		<MyEnumVariable />
	</div> 
);
```

use a wrapping [[Compound Component]] 
```jsx
return ( 
	<div> 
		<h1>Conditional Rendering with enums</h1> 
		<ColorScheme /> 
		{...}
		<ColorScheme state="bar" /> 
		{...}
		<ColorScheme state="foo" /> 
	</div> 
);
```
### References:
https://flexiple.com/react/conditional-rendering-in-react
https://react.dev/learn/conditional-rendering
https://react.dev/learn/javascript-in-jsx-with-curly-braces#using-curly-braces-a-window-into-the-javascript-world