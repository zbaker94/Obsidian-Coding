[[Coding/Principles/Separation of Concerns|Separation of Concerns]] in React applies in much the same way as the general programming principle of the same name. In React, this concept also overlaps with the ideas behind [[Partial Application]]. More specifically, we should build our components to accept only the props and manage only the state that they need to render. Furthermore, a component should only do one thing. This applies from a broader perspective of separating the business logic from the render logic as well as keeping components from taking multiple forms.

A component, when at all possible (most of the time it is, I promise), should not take in props that fundamentally change it's behavior, layout, etc. This is where partial application and even [[Coding/React/Principles/Encapsulation|Encapsulation]] comes in. To truly separate concerns you should group similar behavior or JSX in a component that can be composed together with another component (or multiple others) to build a single component that meets your requirements.

![[Pasted image 20230810104622.png]]


### References:
https://www.freecodecamp.org/news/separation-of-concerns-react-container-and-presentational-components/#whatistheseparationofconcerns