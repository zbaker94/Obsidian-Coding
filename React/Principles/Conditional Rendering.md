Conditional rendering applies to almost any front end framework. At its base level, the concept pertains to hiding or showing an element or elements based on some logic.

Let’s say you have a `PackingList` component rendering several `Item`s, which can be marked as packed or not:

```jsx
function Item({ name, isPacked }) {
  return <li className="item">{name}</li>;
}

export default function PackingList() {
  return (
    <section>
      <h1>Sally Ride's Packing List</h1>
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

Notice that some of the `Item` components have their `isPacked` prop set to `true` instead of `false`. You want to add a checkmark (✔) to packed items if `isPacked={true}`.

You can write this as an [`if`/`else` statement](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/if...else) like so:

```jsx
if (isPacked) {  

	return <li className="item">{name} ✔</li>;  

}  

return <li className="item">{name}</li>;
```

If the `isPacked` prop is `true`, this code **returns a different JSX tree.** With this change, some of the items get a checkmark at the end.

### Conditionally returning nothing with `null` [](https://react.dev/learn/conditional-rendering#conditionally-returning-nothing-with-null "Link for this heading")

In some situations, you won’t want to render anything at all. For example, say you don’t want to show packed items at all. A component must return something. In this case, you can return `null`:

```
if (isPacked) {  return null;}return <li className="item">{name}</li>;
```

If `isPacked` is true, the component will return nothing, `null`. Otherwise, it will return JSX to render.

In practice, returning `null` from a component isn’t common because it might surprise a developer trying to render it. More often, you would conditionally include or exclude the component in the parent component’s JSX.

```jsx
if (isPacked) {  

	return <li className="item">{name} ✔</li>;  

}  

return <li className="item">{name}</li>;
```

Typically you'd want to do this with [[Template Literals]] and the [[Ternary Operator]].

### Logical AND operator (`&&`) [](https://react.dev/learn/conditional-rendering#logical-and-operator- "Link for this heading")

Another common shortcut you’ll encounter is the [JavaScript logical AND (`&&`) operator.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_AND#:~:text=The%20logical%20AND%20(%20%26%26%20)%20operator,it%20returns%20a%20Boolean%20value.) Inside React components, it often comes up when you want to render some JSX when the condition is true, **or render nothing otherwise.** With `&&`, you could conditionally render the checkmark only if `isPacked` is `true`:

```jsx
return (  

<li className="item">  

	{name} {isPacked && '✔'}  

</li>  

);
```

This pattern is less preferred 

### References:
https://flexiple.com/react/conditional-rendering-in-react
https://react.dev/learn/conditional-rendering