## Define Your Components 
For a reusable library of common building blocks, which elements are likely to be reused? Defining these will give you the most bang for your development buck. A few examples of reusable components in most projects would be buttons, inputs, some way to manage form state (and validation), and a common way of styling elements.

When the rubber meets the road and you need to translate a mockup or wireframe to actual code, knowing which reusable pieces you have will help you determine what needs to be built.

For example, the below [[Compound Component]] contains 4 individual pieces. Two of which are reused. 

![[Pasted image 20230816093352.png]]

The component in teal could be a reusable `ProductTableHeader` component. The elements in red could be reusable `ProductTableRow` components. These could even be broken down further to consider the individual `div` elements and styling that make up their layout.

Once you know what components you will need and how that relates to the reusable pieces that have already been built, you can go on to design any new components.

## Define Component Boundaries

First of all, you should to determine the **expectation** from your component. Try to answer questions like:

- What is the use of your component?
- What is the input?
- What is the output?
- Does the component have any side effects?
- Does the component need to track state or is it a [[Controlled Components|controlled component]]?
- What are the dependencies?
- What is the component _NOT_ supposed to do?

At the end of this exercise, you should have a black box representation of your component.

![[Pasted image 20230810135113.png]]

Having a well defined boundary allows you to make architectural decisions as well assumptions about the usage.

## Simple use case first approach

Always try to make the component easy to start off with — minimum or no input from the consumer. This makes it easy for consumers who are _prototyping_ or _evaluating_ your component for further use. It is also very helpful for beginners to start off.

## Basic customization

Consumers will expect you to support simple changes to your component out of the box. For example, it is common for any interactive element to support a `disabled` prop.

### Functionality changes 
These affect the way your component behaves. Think carefully about what changes you want the consumer to make. You may not need to support functionality changes for simple components, but it might make sense for complex components
- Use your black box representation to identify the functionality changes needed.
- For each functionality change identify if it is the responsibility of the component to implement or that of the consumer.

### Style changes

These affect the way your component looks. You cannot make any assumptions about the styles and give the consumer maximum freedom to change it.

Following are a few strategies to make your component easy to style-

- Simple props: Support optional props like size, align, disabled which will allow consumers to make quick adjustments.
- StyleWrapper for more complex or less common styling needs
- Avoid `!important` and multiple [combinators](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors#Combinators) in your CSS. It is very difficult to override something like `.dropdown > .option > button > span`

## Renderers and Components

The goal is to make it very easy for consumers to adapt the component for their use case. In most cases, these changes are UI changes — changing the appearance, adding different styles, etc. In order to achieve this, I split the building blocks into components and renderers.

For dropdown, following are the building blocks:

- `Option`: `OptionComponent` and `OptionRenderer`
- `Trigger`: `TriggerComponent` and `TriggerRenderer`
- `Menu`: `MenuComponent` and `MenuRenderer`

### Renderer

The renderer only cares about how the component looks and other items which need to be displayed. This has all the styling attached and accepts the bare minimum props required for display.

### Component

These handle the business logic for the particular block — event handlers, refs, roles, etc. These are just containers and do not have any styles. This abstracts away the working logic from rendering and consumers can simply swap out renderers without affecting the functionality. Advanced consumers, who want complete control, can replace this component and handle everything themselves.

This splitting allows for quicker changes and promotes modular code, improving the testability of your code.

## Custom props

Your component will render certain HTML elements. These elements will have attributes which you programmed for.

You should also allow the consumer to pass in custom props for these HTML elements. This can be useful for someone who wants to add additional event handlers or tags like aria which you might not have included out of the box. 

When working with a component library, it is important to determine if a custom prop is needed or if it is the responsibility of the consumer to handle in a different way (ie: composing your component with one of their own).

### Keyboard

Your component should be usable by keyboard. This means using the correct type of HTML tags (i.e. button and a for interactive elements) and handling focus in case of custom widgets.

You can also decide to handle keyboard events yourself within JavaScript. You might miss out on default browser behaviors and other benefits which come along with keyboard focus.

## Enforcing certain usage

It is easy for usage anti-patterns to creep in when a library is consumed by people who do not know how and why it was built. As a developer, your job is to convey this information and enforce proper usage —

- Component design: Let’s take the dropdown example. You can choose to pass options as an array of objects or as children

```js
// Method 1
const options = [{id: 1, label: ‘opt 1’}, {id:2, label:’opt 2’}];

<Dropdown options={options} onClick={e => console.log(e)} />


// Method 2
<Dropdown>
  <Option id=‘1’>opt 1</Option>
  <Option id=‘2’>opt 2</Option>
</Dropdown>
```

Your code might only expect `Option` to be passed as children but, _Method 2_ might create a false expectation for the consumer that they can pass anything and the component might break. An anti-pattern usage could be -

```js
// Anti-pattern usage which might break your component
<Dropdown>
  <div class=‘section-1’>
    <Option id=‘1’>opt 1</Option>
    <Option id=‘2’>opt 2</Option>
  </div>
  <div class=‘section 2’>
    <Option id=‘1’>opt 1</Option>
    <Option id=‘2’>opt 2</Option>
  </div>
</Dropdown>
```

You should be able to determine the best way to use your component and enforce that by the way you design your component.

- ***[[Proptypes]]*** are a great way to type check the props your component accepts and prevent basic mistakes made by consumers.
- ***Documentation*** is one the easiest way to convey information to consumers. It is crucial to highlight **what** is the purpose component along with usage examples.
- ***Test cases*** provide a good reference point to consumers for what and what not to do.

## Escape Hatches
If all else fails, it is prudent for the developer of reusable code to provide an "escape hatch". An escape hatch is an intentional hole in a library or frameworks  abstraction that allow the developer to access the underlying platform primitive. For example, React has the [`ref` property](https://reactjs.org/docs/refs-and-the-dom.html) to get ahold of a component’s corresponding DOM element, exposing the underlying platform primitive.


### References:
https://ankit-m.github.io/blog/building-highly-customizable-react-components/
https://surma.dev/things/cost-of-convenience/