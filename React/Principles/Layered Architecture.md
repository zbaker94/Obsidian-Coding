
Layered architecture is a principle that applies in many areas of software development and specifically when writing code for others to use. The desire to provide a set of building blocks that do not force a developer to fully opt in or opt out of your tools increases the likelihood of long term adoption. This makes your code more [[Reusable vs Useful|Useful]]. Furthermore, a layered architecture makes your code more easily extendable in the future and therefore simpler to maintain with less risk of painting yourself into a corner.


## What is it?
Long story short, layered architecture is the idea that having multiple layers of abstraction will result in the above benefits. To do so, you must separate what differs from what stays the same in a nested manner. 

For example, say we have the classic React example of a counter application. This application needs several buttons for *increment*, *decrement*, *add 2*, and *clear*. You might handle it like so

##### Counter Component
```jsx
const actionLookup = new Set(["INCREMENT", "DECREMENT", "INCREMENTBYTWO", "CLEAR"])
const Counter = () => {
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
				Increment by Two
			</button>
			<button style={buttonStyle} 
			onClick={()=>dispatch("CLEAR")}>
				Increment
			</button>
		</div>
	)
}
```

While (in theory) this works just fine, you may feel that this is a ton of repeated code for such simple functionality. All of the buttons are doing more or less the same thing. Same styles, all are dispatching to the reducer, etc. So let's fix that.

Your first urge may be to do something like the following

##### ActionLookup [[Enum]] 
```jsx
const actionLookup = {
	INCREMENT: {
		label: "Increment",
		callback: (value) => value + 1
	},
	DECREMENT: {
		label: "Decrement",
		callback: (value) => value - 1
	},
	INCREMENTBYTWO: {
		label: "Increment By Two",
		callback: (value) => value + 2
	},
	CLEAR: {
		label: "Clear",
		callback: (value) => 0
	} 
}
```

##### CounterProvider Component
```jsx
export const CounterContext = createContext();

export const useCounterProvider = () => {
	return useContext(CounterContext)
}

export const CounterProvider = ({children}) => {
	const [counterValue, setCounterValue] = useState(0);

	return <CounterContext.Provider 
	value={{
		setCounterValue,
		counterValue
	}}>
		{children}
	</ CounterContext.Provider>
}
```
##### Counter Button Component
```jsx
import actionLookup from "./actionLookup"

const CounterButton = ({action}) => {
	const {setCounterValue} = useCounterProvider()
	
	const buttonStyle = useMemo(() => ({
		backgroundColor: "red"
	}))

	const onClick = useCallback(() => {
		if(!actionLookup?.[action]){
			const error = 
			`ERROR: action passed to reducer must be one of \
			${Object.keys(actionLookup).join(', ')}`
			return console.error(error)
		}

		const {callback} = actionLookup[action]?.callback
		
		setCounterValue(prev => {
			const newValue = callback(prev)
			return newValue
		})
	})
	const label = useMemo(() => actionLookup?.[action]?.label || 
	"No Label")
	
	return (
		<button style={buttonStyle} onClick={onClick}>{label}</button>
	)
}
```

##### New and Improved Counter Component
```jsx
const Counter = () => {
	return (
		<CounterProvider >
			<CounterButton action="INCREMENT" />
			<CounterButton action="DECREMENT" />
			<CounterButton action="INCREMENTBYTWO " />
			<CounterButton action="CLEAR" />
		</ CounterProvider >
	)
}
```

Fairly layered, right? Well what happens when something needs to change?

#### New Requirements 
Alright so you have your DRY counter component that accurately tracks the counter value. If you need to add a new button, you easily can by adding a new action. 

What happens, then when a new requirement is added by the end user that they want a custom, per-button message to be logged when the user clicks the button?

In this case you may approach the change in one of the following ways.

- Add a new ‘LOG’ action and in the provider to simply do a console log with that action. This is kind of weird because we’re not actually modifying the state, but it would fit with our existing pattern.
- Create a new `<LogCounterButton />` component for this one use case. That feels a little off to me: we’re basically going to be duplicating the button element and button styling, meaning if we change the implementation in one place we need to change it in the other place. Not DRY at all.
	- We *could* extend the `<CounterButton />` to accept an `onClick` or `onClickSideEffect` function that is called from the built-in `onClick` so that our  `<LogCounterButton />` can take in the log message, render the `<CounterButton />` and pass the 

### References:
https://surma.dev/things/cost-of-convenience/ 
https://jesseduffield.com/React-Abstractions/