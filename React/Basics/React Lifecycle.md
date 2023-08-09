A React component undergoes three phases in its lifecycle: **mounting**, **updating**, and **unmounting**.

- The **_mounting** phase_ is when a new component is created and inserted into the DOM or, in other words, when the life of a component begins. This can only happen once, and is often called the “initial render.”
	- In newer versions of React, when in dev mode, components mount and *unmount* twice as part of the initial render to insure no unintended side effects (see [[Idempotence in React Effects]])
- The **_updating** phase_ is when the component updates or re-renders. This reaction is triggered when the props are updated or when the state is updated. This phase can occur multiple times.
	- This can trigger [[UseEffect]] callbacks. 
	- React tries to batch re-renders when multiple changes are detected in [[The Virtual DOM]].
	- Re-renders can also occur if a parent component Re-renders and the child is not properly memoized.
	- [[The React Context API]] complicates the update cycle since a value in a Context Provider somewhere up [[The Component Tree]] may cause Re-renders in other Components  elsewhere in the tree. 
- The last phase within a component's lifecycle is the **_unmounting** phase_, when the component is removed from the DOM.
	- This can trigger [[UseEffect]] callbacks. 
	- Any state updates made in a component after it unmounts are invalid and can indicate a memory leak

![[Pasted image 20230809124656.png]]
### References:
https://retool.com/blog/the-react-lifecycle-methods-and-hooks-explained/