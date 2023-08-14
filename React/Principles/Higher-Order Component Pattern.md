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

However, this pattern is not common and is rather fragile. Alternatively we can use a render prop:

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

If you want the wrapping component to handle rendering, you can simple pass the component as a prop.

```jsx
const WrappingComponent = ({WrappedComponent}) => {
	const style = `backgroundColor: red;`

	return <div>
		<WrappedComponent style={style}/>
	</div>
}
```