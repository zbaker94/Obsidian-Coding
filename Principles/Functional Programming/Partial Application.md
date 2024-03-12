In [computer science](https://en.wikipedia.org/wiki/Computer_science "Computer science"), **partial application** (or **partial function application**) refers to the process of fixing a number of arguments to a function, producing another function of smaller [arity](https://en.wikipedia.org/wiki/Arity "Arity"). Partial application is sometimes incorrectly called [[Currying]], which is a related, but distinct concept.

``` js
const add = (a) => {
	return (b) => {
		return a + b
	}
}

const addOne = add(1)

console.log(add(5)(10)) // 15
console.log(addOne(10)) // 11
```



### References:
https://en.wikipedia.org/wiki/Partial_application