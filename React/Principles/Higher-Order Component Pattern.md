A higher-order component (HOC) is an advanced technique in React for reusing component logic. HOCs are not part of the React API, per se. They are a pattern that emerges from React’s compositional nature.

Concretely, **a higher-order component is a function that takes a component and returns a new component.**

Higher-order components are not commonly used in modern React code. However, we can use a similar pattern using the *Children* prop.

```jsx
<WrappingComponent>
	<WrappedComponent />
</WrappingComponent>
```

```jsx
const WrappingComponent = ({children}) => {
	const style = `backgroundColor: red;`

	return <div>
		{Children.map(children, (child, index) =>
        cloneElement(child, {
          style;
        })
      )}
	</div>
}
```

However, this pattern is not common and is rather fragile due in part to the fact that any valid React element could be passed to `Children`. 

Alternatively we can use a render prop:

```jsx
const WrappingComponent = ({wrappedComponentData}) => {
	const [selectedItem, setSelectedItem] = usestate(wrappedComponentData[0])
	
	const renderWrappedComponent = (wrappedComponent) => {
		const backgroundColor = selectedItem.id === wrappedComponent.id ? "red" : "white"
		return <WrappedComponent id={wrappedComponent.id} backgroundColor={backgroundColor} />
	}

	return <div>
		{wrappedComponentData.map((wrappedComponent, index) => {    
			return renderWrappedComponent(wrappedComponent);  
		})}
	</div>
}
```

If you want the wrapping component to handle rendering while still indicating (via the name / [[Proptypes]] ) what should be passed as a valid child, you can simply pass the component as a prop.

```jsx
const WrappingComponent = ({WrappedComponent}) => {
	const style = `backgroundColor: red;`

	return <div>
		<WrappedComponent style={style}/>
	</div>
}
```

It is worth noting that there are some very important differences that occur depending on how you render a component that has been assigned to a variable. Component variables come in two basic flavors: functions and pre-rendered JSX

###### Function Component Variable
```jsx
const Component = () => {
	return <div>Hello World</div>
}

<Parent Child={Component} />
```

###### Pre Rendered Component Variable
```jsx
const component = () => {
	return <div>Hello World</div>
}

<Parent Child={component()}/>
```

this is the same as 
```jsx
<Parent Child={<div>Hello World</div>}/>
```

The key takeaway is that when the component renders and rerenders is different for each of these. This can cause strange behavior if not done on purpose. In most cases, passing the component as a function to be rendered by the child is the better option.

The distinction here may seem obvious in this context. However we often see JSX being pre-rendered on accident when a component (usually declared inside of another component. Bad practice leads to bad practice leads to bugs) is returned from [[UseMemo]].

Since useMemo takes a callback and then memoizes the rerturn of that callback, many developers do the following

```jsx
const Component = useMemo(() => {
	return <div>Hello World</div>
}, [])


<Parent Child={Component} />

```

This pre-renders the component as part of the [[Hooks]] lifecycle vs allowing the child to render the component on its own terms. This will cause issues when attempting to do so like this

```jsx
return <Child />
```

since `Child` is rendered JSX, this syntax will not work.

The best practice is to not declare components inside of other components and if you do, make sure that your useMemo returns a function *unless* you intend to pre-render your JSX.