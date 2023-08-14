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

}
```