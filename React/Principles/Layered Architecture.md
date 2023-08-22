
Layered architecture is a principle that applies in many areas of software development and specifically when writing code for others to use. The desire to provide a set of building blocks that do not force a developer to fully opt in or opt out of your tools increases the likelihood of long term adoption. This makes your code more [[Reusable vs Useful|Useful]]. Furthermore, a layered architecture makes your code more easily extendable in the future and therefore simpler to maintain with less risk of painting yourself into a corner.


## What is it?
Long story short, layered architecture is the idea that having multiple layers of abstraction will result in the above benefits. To do so, you must separate what differs from what stays the same in a nested manner. 

For example, say we have the classic React example of a counter application. This application needs several buttons for *increment*, *decrement*, *add 2*, and *clear*. You might handle it like so

##### Counter Component
```jsx
const actionLookup = new Set(["INCREMENT", "DECREMENT", "INCREMENTBYTWO", "CLEAR"])
const counter = () => {
	const counterReducer = useCallback((value, action) => {
		// handle invalid action
		if(!actionLookup.has(action)){
			const error = 
			`ERROR: action passed to reducer must be one of \
			${[...actionLookup].join(', ')}`
			return console.error(error)
		}
		
		switch(action) {  
		  case "INCREMENT":  
		    return value + 1
		  case "DECREMENT":  
		    return value - 1
		 case "INCREMENTBYTWO":
			 return value + 2
		 case "CLEAR":
			 return 0
		 default:  
		    console.warn(`action "${action}" passed to reducer \ 
		    was found in lookup but no handler exists.`)
		}
	})
	const [counterValue, dispatch] = useReducer(counterReducer, 0);

	const buttonStyle = useMemo(() => ({
		backgroundColor: "red"
	}))

	return (
		<div>
			<button style={buttonStyle} 
			onClick={()=>dispatch("INCREMENT")}>
				Increment
			</button>
			<button style={buttonStyle} 
			onClick={()=>dispatch("DECREMENT ")}>
				Increment
			</button>
			<button style={buttonStyle} 
			onClick={()=>dispatch("INCREMENTBYTWO ")}>
				Increment
			</button>
			<button style={buttonStyle} 
			onClick={()=>dispatch("CLEAR")}>
				Increment
			</button>
		</div>
	)
}
```

While (in theory) this works just fine, you may feel that this is a ton of repeated code for such simple functionality. All of the buttons

### References:
https://surma.dev/things/cost-of-convenience/ 
https://jesseduffield.com/React-Abstractions/