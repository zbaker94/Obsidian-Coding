The compound component pattern is a pattern that encloses the state and the behavior of a group of components but still gives the rendering control of its various parts back to the external user. The objective of compound components is to provide a more expressive and flexible API for communication between the parent and the child components.


```jsx
  return (
    <Menu>
      <MenuButton>Actions</MenuButton>
      <MenuList>
        <MenuItem>Download</MenuItem>
        <MenuLink to="/view">View</MenuLink>
      </MenuList>
    </Menu>
  );
```

The `Menu` tag works together with the `MenuList` and `Option` components which is used for a drop-down menu to select items. Here the `Menu` manages the state of the UI, then the `<MenuItem>` elements inform how the `<Menu>` should display. Compound components in React are used to build a declarative UI component which helps to avoid prop drilling.

In some cases, further nesting of components is required such that the direct children of the parent may not be the elements we want to share props with. [[The React Context API]] can be used to avoid prop drilling in this case. 

```jsx
return (
	<FlyOutProvider>
	    <FlyOut>
		    // FlyOut cannot pass props directly to its children
	      <div>
	        <FlyOut.Toggle />
	        <FlyOut.List>
	          <FlyOut.Item>Edit</FlyOut.Item>
	          <FlyOut.Item>Delete</FlyOut.Item>
	        </FlyOut.List>
	      </div>
	    </FlyOut>
    </ FlyOutProvider>
  );
```

Using providers also shifts the responsibility of controlling which props a child receives from the parent to the child component. Which you may or may not want.
### References:
https://www.smashingmagazine.com/2021/08/compound-components-react/
https://blog.logrocket.com/understanding-react-compound-components/