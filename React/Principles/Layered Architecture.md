
Layered architecture is a principle that applies in many areas of software development and specifically when writing code for others to use. The desire to provide a set of building blocks that do not force a developer to fully opt in or opt out of your tools increases the likelihood of long term adoption. This makes your code more [[Reusable vs Useful|Useful]]. Furthermore, a layered architecture makes your code more easily extendable in the future and therefore simpler to maintain with less risk of painting yourself into a corner.


## What is it?
Long story short, layered architecture is the idea that having multiple layers of abstraction will result in the above benefits. To do so, you must separate what differs from what stays the same with an eye towards flexibility. A reusable component is no good if a future requirement means having to rebuild it. 

## In Practice
![[Pasted image 20230822120302.png]]
We have a basic input that wraps the html input element.

![[Pasted image 20230822120325.png]]
We have a form component that renders it's children, keeps track of various state values, and provides functions to set and reset the state (via the useContext hook).
![[Pasted image 20230822120403.png]]
So how do we get these two elements to communicate with each other? 

The most basic answer is to simply wrap whichever inputs need form access in a single component that is in turn wrapped by the provider. We then just connect the relevant form methods to the inputs.

```jsx
```
### References:
https://surma.dev/things/cost-of-convenience/ 
https://jesseduffield.com/React-Abstractions/