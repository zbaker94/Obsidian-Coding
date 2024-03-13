In react, abstraction applies slightly differently than in object oriented languages. Abstraction in React refers to a set of techniques that help reduce prop drilling and increase reuse.

Two of these techniques are [[Shell Component]] and [[Compound Component]] .

These two patterns can even be combined.
```jsx
const TableShell = ({headerText, TableProp, ControlsProp}) => {
...
return <TableShellProvider>
	<Header>{headerText}</Header>
	<div>
		<TableProp />
	</div>
	<div>
		{
			!ControlsProp ? null : (
				<TableControls controls={ControlsProp}/>
			)
		}
	</div>
</TableShellProvider>
}
```

In this example, we provide "slots" for the consuming code to pass a table component and a table controls component that are rendered by the shell. We also define a context provider that will be responsible for handling the state of the compound component and its children. This allows us to specify the general layout and behavior of the TableShell while providing a means for the developer to customize the contents.