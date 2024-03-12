In React, the principle of Immutability is similar to the general software development concept of [[Coding/Principles/Immutability|Immutability]]. More specifically, this applies in two areas of React. **State** and **Props**.

- For **State** we do not want to mutate it directly.
	- This mostly goes without saying, but using *setState* in [[Class Components]] and the setter from [[useState]] in [[Functional Components]] should be the only way a state value gets updated in your components.
	- This also goes for state that is passed around and values that are passed into a component and later set as state.
		- For example if "foo" is passed to a callback that sets state in a parent component to the value of "foo", we need to make sure that we both do not mutate "foo" if at all possible and at the very least we make a [[Deep Copy]] of "foo" before mutating and setting it in state.
		- If we do not, unexpected mutations of "foo" later on could cause the value held in state to change outside of the normal [[React Lifecycle]] due to being [[Pass by Reference]].
- For **Props** the same principle mostly applies: Do not mutate props unless you first reassign them
Following this practice insures that if a prop is passed in, it is used in that current form. This prevents having to dive into many components when debugging etc.



### References: