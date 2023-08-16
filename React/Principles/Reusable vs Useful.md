Reusable component libraries are tricky to manage and maintain. Not all components are reusable but most are useful. Meaning that they were built to serve some purpose. So, rather than writing “reusable components” from the jump you should try to write “useful components” which are good at serving their purpose. You should write them not to be reused out of the box, necessarily but to be abstract and composable enough to serve their purpose in a variety of implementations. Design your components so that it will be practical for you or another developer to adapt it for different requirements.

For example, if I have a need to lay out a page, I may simply do so with basic html `div` elements and use css to style them with [[Flexbox]] or [[CSS Grid]]. However, if I am thinking of reuse (or maybe after doing the same process 2+ times), I may consider creating a set of components that abstract and formalize the behavior of my layout.

```jsx
const FlexContainer = ({children}) => {
	return (
		<StyleWrapper display="flex">
			<div>{children}</div>
		</StyleWrapper> 
	)
}

const FlexItem = ({children}) => {
	<div>{children}</div>
}
```

As a first iteration, these two components are not *reusable* for every conceivable use case but are *useful* for speeding up my development process. Further down the line, additional props could be added to mimic the options available in [[Flexbox]] such as  `justify-content` or `flex-grow`. We might even include some defaults that match our common usage. We could even build a layer of abstraction on top of these components to follow, for instance, a 12 column layout pattern like is used in [The MUI Grid](https://mui.com/material-ui/react-grid/) 
### References:
https://blog.bitsrc.io/do-we-really-use-reusable-components-959a252a0a98
[[Coding/React/Principles/Separation of Concerns|Separation of Concerns]]
