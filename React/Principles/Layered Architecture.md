
Layered architecture is a principle that applies in many areas of software development and specifically when writing code for others to use. The desire to provide a set of building blocks that do not force a developer to fully opt in or opt out of your tools increases the likelihood of long term adoption. This makes your code more [[Reusable vs Useful|Useful]]. Furthermore, a layered architecture makes your code more easily extendable in the future and therefore simpler to maintain with less risk of painting yourself into a corner.


## What is it?
Long story short, layered architecture is the idea that having multiple layers of abstraction will result in the above benefits. To do so, you must separate what differs from what stays the same in a nested manner. 

## In Practice
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

While (in theory) this works just fine, you may feel that this is a ton of repeated code for such simple functionality. All of the buttons are doing more or less the same thing. Same styles, all are dispatching to the reducer, etc. So let's fix that to separate concerns.

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
##### CounterButton Component
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

Fairly layered and separate, right? Well...

#### New Requirements 
Alright so you have your DRY counter component that accurately tracks the counter value. If you need to add a new button type, you easily can by adding a new action to the lookup. 

What happens, then, when a new requirement is added by the end user that they want a button that logs the counter value when the user clicks?

We want to maintain our styling and any other `CounterButton` specific code for this new button but we now have behavior that does not directly mutate the counter itself. 

In this case you may approach the change in one of the following ways.

- Add a new ‘LOG’ action and in the provider to simply do a console log with that action. This is kind of weird because we’re not actually modifying the state, but it would fit with our existing pattern.
- Create a new `<LogCounterButton />` component for this one use case. That feels a little off to me: we’re basically going to be duplicating the button element and button styling, meaning if we change the implementation in one place we need to change it in the other place. Not DRY at all.
	- We *could* extend the `<CounterButton />` to accept an `onClick` or `onClickSideEffect` function that is called from the built-in `onClick` so that our  `<LogCounterButton />` can take in the log message, render the `<CounterButton />`, and pass the console log as the `onClickSideEffect`
	- This is tricky because it enforces [[Coding/Principles/Separation of Concerns|Separation of Concerns]] while at the same time giving a potential developer down the line additional cognitive load when determining which component to use.
- Add a new string prop to the `<CounterButton />`  component for when you want to log something.

Given these options, the best solution given our current situation is likely number 3. Not only will it solve the current use case, it can also be used by the other buttons in addition to their actions if we need to add per-action logging down the line.

As part of this change, we will also need to add an optional label prop to our button for when we do not pass an action.

##### New and Improved CounterButton Component 
```jsx
import actionLookup from "./actionLookup"

const CounterButton = ({action, log, label}) => {
	const {setCounterValue, counterValue} = useCounterProvider()
	
	const buttonStyle = useMemo(() => ({
		backgroundColor: "red"
	}))

	const onClick = useCallback(() => {
		if(action && !actionLookup?.[action]){
			const error = 
			`ERROR: action passed to reducer must be one of \
			${Object.keys(actionLookup).join(', ')}`
			return console.error(error)
		}
		if(action){
			const {callback} = actionLookup[action]?.callback
		
			setCounterValue(prev => {
				const newValue = callback(prev)
				return newValue
			})
		}

		if(log){
			const logMessage = log(counterValue)
			console.log(logMessage)
		}
	})
	const label = useMemo(() => actionLookup?.[action]?.label ||
	 label ||
	 "No Label")
	
	return (
		<button style={buttonStyle} onClick={onClick}>{label}</button>
	)
}
```

##### Newer and Improved Counter Component
```jsx
const Counter = () => {
	return (
		<CounterProvider >
			<CounterButton action="INCREMENT" />
			<CounterButton action="DECREMENT" />
			<CounterButton action="INCREMENTBYTWO " />
			<CounterButton action="CLEAR" />
			<CounterButton log={(currentValue) => `Current value is\
			${currentValue}`} />
		</ CounterProvider >
	)
}
```

Still relatively clean and reusable.

#### Another new requirement 
Our last new requirement comes down the line. This time, the user wants to keep the existing functionality but they also want an input that they can type a number into and then press a button to add that number to the counter.

This should be relatively easy. We can add a metadata prop and then an `"ADD"` action in the lookup.

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
	},
	ADD: {
		label: "Add",
		callback: (value, metadata) => value + +metadata.value
	} 
}
```

CounterButton Component 
```jsx
import actionLookup from "./actionLookup"

const CounterButton = ({action, log, label, metadata}) => {
	const {setCounterValue, counterValue} = useCounterProvider()
	
	const buttonStyle = useMemo(() => ({
		backgroundColor: "red"
	}))

	const onClick = useCallback(() => {
		if(action && !actionLookup?.[action]){
			const error = 
			`ERROR: action passed to reducer must be one of \
			${Object.keys(actionLookup).join(', ')}`
			return console.error(error)
		}
		if(action){
			const {callback} = actionLookup[action]?.callback
		
			setCounterValue(prev => {
				const newValue = callback(prev, metadata)
				return newValue
			})
		}

		if(log){
			const logMessage = log(counterValue)
			console.log(logMessage)
		}
	})
	const label = useMemo(() => actionLookup?.[action]?.label ||
	 label ||
	 "No Label")
	
	return (
		<button style={buttonStyle} onClick={onClick}>{label}</button>
	)
}
```

##### Counter Component
```jsx
const Counter = () => {
	const [inputValue, setInputValue] = useState("")
	return (
		<CounterProvider >
			<CounterButton action="INCREMENT" />
			<CounterButton action="DECREMENT" />
			<CounterButton action="INCREMENTBYTWO " />
			<CounterButton action="CLEAR" />
			<CounterButton log={(currentValue) => `Current value is\
			${currentValue}`} />
			
			<input value={inputValue} onChange={
				(e) => setInputValue(e.target.value)
			} />
			
			<CounterButton action="ADD" metadata={{
					value: inputValue
				}} />
		</ CounterProvider >
	)
}
```

### When DRY goes wrong
At this point you may be getting a feeling that all this work may not be much better than the previous implementation. Yes, that code was not DRY but at least it was flexible. We started by correctly abstracting the common code for the button. The mistake was coupling the specific dispatch / counter logic to that abstract button. In doing so, we incorrectly assumed that the parent of the button would only ever need to express intent in the form of a string action. This ignored the possibility of future new requirements.

We knew this was true when we added the `log` prop, and so to defend our  fragile abstraction we entertained a hypothetical future where we might want to both log _and_ dispatch an action, despite there being no known use case for it right _now_.

When we needed to pass more information to one of our actions, we had to express it in a way that would play nicely with the other use cases: by only conditionally adding a metadata key if the prop was present.

Even the theoretical benefit of hiding the knowledge of the available actions from the parent falls apart because it still needs to know what the type of the action is (and any metadata required) to pass down as a prop. For every new action we add to modify the counter in some way, we will need to make a change in the lookup, in Counter, and probably in CounterButton if the use case is once more slightly different to the ones that came before.

This is where we finally get into truly layered architecture.

### The thing you came here for
Our failure in approach in the above example came from incorporating too much abstraction too soon. There was a need to [[Coding/React/Principles/Encapsulation|Encapsulate]] the button styling and logic. But to keep our options open we should not have brought the counter-specific logic along for the ride. Alternatively, we could have approached the problem with usability in mind:




### References:
https://surma.dev/things/cost-of-convenience/ 
https://jesseduffield.com/React-Abstractions/