## JS Tweaks and Tricks

  

This section is meant for you to be able to grasp some not so common but so useful features of javascript.

## **How to create a variable: var, let, const**

As a general rule we should always use `const`. Use `let` when you need to modify the variable value (remember you can modify object values and arrays even if they are const). And don't ever use `var` as this creates a function/global variable.

- const: creates a constant value that can't be modified. Objects and arrays are references so you just can't change the reference but you can change the content.
- let: creates a variable that can be changed limited to the scope in which it was created.
- var: creates a variable that can be changed with a function/global scope.

### **Reference and value**

In javascript is basic to understand what do we mena with value and reference. A variable can be passed as value or as reference. 
Objects and array are ALWAYS references and the rest of type of variable are ALWAYS passed as values.

This is relevant cause if you change the value of a param received are reference it will change the original param.

Reference:
```js
let a = { holi: true }
const dangerousFunction = (param) => {
	param.holi = false;
}
dangerousFunction(a);
console.log(a.holi ? 'holi' : 'hate you!')
// hate you!
```
Value:
```js
let holi = true
const dangerousFunction = (param) => {
	param.holi = false;
}
dangerousFunction(holi);
console.log(holi ? 'holi' : 'hate you!')
// holi
```

### **Falsable values**

We need to be aware that some values are used as false in case we use then in a boolean condition such as an if o casting it to boolean. This values are:
- false
```js
console.log(false ? 'no falsable' : 'falsable')
// falsable
```
- 0
```js
console.log(0 ? 'no falsable' : 'falsable')
// falsable
```
- ""
```js
console.log("" ? 'no falsable' : 'falsable')
// falsable
```
- null
```js
console.log(null ? 'no falsable' : 'falsable')
// falsable
```
- undefined
```js
console.log(undefined ? 'no falsable' : 'falsable')
// falsable
```

### **Self-invoking functions**

What if you need to create a function to just to run it once? You dont even need to name the function, you can use arrow function syntax and let the function to invoke itself. 

```js
const runMe = () => { console.log("i'm doing something")}
runMe();
```
You could just:
```js
(() => { console.log("i'm doing something")})();
```

### **Functions returning functions**

You must know that functions can return anything, even another functions! How is this useful tho?

- Function wrapping:

Imagine that you have a set of functions that have the some structure, same start, same end. If you see repetitive patterns in general you should refactor the code but in this case as the middle code is different it seem different, but it's not, you can use a function to wrap you function!

Original code:
```js
const  sum  = (a, b) => {
	try {
		return  a  +  b;
	} catch (e) {
		console.log(e);
	}
};
const  diff  = (a, b) => {
	try {
		return  a  -  b;
	} catch (e) {
		console.log(e);
	}
};
```

Code refactored applying function wrapping:
```js
const wrapper = (fn) => (...args) => {
	try {
		return  fn(...args)
	} catch (e) {
		console.log(e);
	}
}
const  sum  = wrapper((a, b) => { 
	return a  +  b 
});
const  diff  = wrapper((a, b) => { 
	return a  -  b 
});
```

You can can further simplify this by removing useless return:
```js
const wrapper = (fn) => (...args) => {
	try {
		return  fn(...args)
	} catch (e) {
		console.log(e);
	}
}
const  sum  = wrapper((a, b) => a  +  b);
const  diff  = wrapper((a, b) => a  -  b);
```

- Common parameters
  
  Imagine you have a set of call to a function with some repetitive parameters, in the example I'm using just 2 but it can be a lot of them.
```js
const localizedPrice1 = getLocalizedPrice({ country, language, price: price1 })
const localizedPrice2 = getLocalizedPrice({ country, language, price: price2 })
const localizedPrice3 = getLocalizedPrice({ country, language, price: price3 })
const localizedPrice4 = getLocalizedPrice({ country, language, price: price4 })
```

You can avoid the repetitive usage of the same variables by creating a function that injects them in the final function:
```js
const getSimpleGetLocalizedPrice = ({country, language }) => (price) => getLocalizedPrice({ country, language, price })
const simpleGetLocalizedPrice = getSimpleGetLocalizedPrice({ country, language })


const localizedPrice1 = simpleGetLocalizedPrice(price1)
const localizedPrice2 = simpleGetLocalizedPrice(price2)
const localizedPrice3 = simpleGetLocalizedPrice(price3)
const localizedPrice4 = simpleGetLocalizedPrice(price4)
```
You don't save line but you wont need to send the same parameters over and over so you save time and maybe add readability.

Expert solution, Self-Invoking Functions:
```js
const simpleGetLocalizedPrice = ({country, language }) => (price) => getLocalizedPrice({ country, language, price })({ country, language })

const localizedPrice1 = simpleGetLocalizedPrice(price1)
const localizedPrice2 = simpleGetLocalizedPrice(price2)
const localizedPrice3 = simpleGetLocalizedPrice(price3)
const localizedPrice4 = simpleGetLocalizedPrice(price4)
```

### **Constant object vs switch**

I've seen so many times people using switch to do stuff depending on a variable and as we will see here it is inefficient and not as clear as it could be.

Original code:
```js
switch(variable) {
	case 'family':
		return familyPrice(price);
	case 'offer':
		return offerPrice(price);
	case 'new':
	case 'regular':
		return regularPrice(price);
}
```

We can migrate this to an object and access to the value directly avoiding all the checks the program would be doing in the previous example.

```js
const priceMap = {
	family: familyPrice,
	offer: offerPrice,
	new: regularPrice,
	regular: regularPrice
}
return priceMap[variable](price)
```
I've used a function in this example but more commonly would be just a variable, so you wont need to execute anything, it would be just `return priceMap[variable]`


### **Create object with a parameter from a variable**

Sometimes you need to create an object and the properties depends on a variable, how can we do this?

Usually starters do the following:
```js
const variable = 'objectKey';
const object = {}
object[objectKey] = 'someMeaningfulValue';
```
But you can improve this:
```js
const variable = 'objectKey';
const object = { [objectKey]: 'someMeaningfulValue' }
```
In larger objects with more dynamic keys this will be very useful, as it allows you for example to return the object directly from a function instead of instantiating, adding the key and returning.
From:
```js
const variable = 'objectKey';
const func = () => {
	const object = {}
	object[objectKey] = 'someMeaningfulValue';
	return object;
}
```
To:
```js
const variable = 'objectKey';
const func = () => ({ [objectKey] = 'someMeaningfulValue' })
```

### **Ternary operator**

Ternary operator is just a simplification (sugar syntax) of an if else. 
Let me share and if else:
```js
if (condition) {
	console.log('the condition is true');
} else {
	console.log('the condition is false');
}
```
You can simplify this with ternary operator, it will to just the same with less code:
```js
condition ? console.log('the condition is true') : console.log('the condition is false');
```

Expert Solution, refactor repetitive code:
```js
 console.log(condition ? 'the condition is true': 'the condition is false');
```

### **?? and ||**

This are very useful operators but people struggle to differentiate them.
```js
 const a = true ?? 'nope';
 console.log(a);
 // true
  const a = false ?? 'nope';
 console.log(a);
 // false
  const a = true || 'nope';
 console.log(a);
 // true
  const a = false || 'nope';
 console.log(a);
 // 'nope'
```

As you can see it's different to use one or another. 

?? is meant to return you the left part if it exists and the right if it doesn't.
So:
```js
const condition = true;
 const a = (condition !== null && condition !== undefined )? condition : 'nope';
```
Will be the same as:
```js
 const a = true ?? 'nope';
```
?? operator is just sugar syntax for a ternary operator assigning the condition as the result if it's not undefined or null.

|| is meant to return the the left part if its not falsable and the right if it is.
So:
```js
 const a = true ? true : 'nope';
```
Will be the same as:
```js
 const a = true || 'nope';
```
|| operator is just sugar syntax for a ternary operator assigning the condition as the result if it's true.

### **??= and ||=**

There are some modern operator relatively recently added that can further simplify things. If you have a variable that you wanna keep its value or update it depending on if its empty, null, undefined, falsable... these operators can help you.

??=
```js
let a = true;
a = a ?? 'nope';
```
Will be the same:
```js
let a = true;
a ??= 'nope';
```

||=
```js
let a = true;
a = a || 'nope';
```
Will be the same:
```js
let a = true;
a ||= 'nope';
```

Summarizing:
- you can use ??= to assign a value to a variable in case it is not defined (null or undefined) or leave the value if it's defined.
- you can use ||= to assign a value to avariable in case it is falsable or leave the value if its defined

### **Templates**

Before the templates were added to javascript you needed to concatenate string with + and it can become a little crazy with more complex logic. So they added templates!
From:
```js
const a = "we";
const b = "are";
const c = "concatenating";
const d = "as";
const e = "you";
const f = "can";
const g = "see";
console.log( a + " " + b + " " + c + " " + d + " " + e + " " + f + " " + g );
```
To:
```js
const a = "we";
const b = "are";
const c = "concatenating";
const d = "as";
const e = "you";
const f = "can";
const g = "see";
console.log(`${a} ${b} ${c} ${d} ${e} ${f} ${g} `);
```
Expert/Bonus solution, join:
```js
const a = "we";
const b = "are";
const c = "concatenating";
const d = "as";
const e = "you";
const f = "can";
const g = "see";
console.log([a,b,c,d,e,f,g].join(" "));
```

### **Object cloning**

As you saw in the previous, let's call it chapter, objects can't be passed as value. So what if I waht to use the same object but with some changes without changing the original object? Here comes the cloning. There are 3 ways of cloning:

- spread:
```js
{ ...object }
```
- Object.assign:
```js
Object.assign({}, food)
```
- JSON.parse and JSON.stringify:
```js
JSON.parse(JSON.stringify(food))
```
Be careful with this method cause it wont work for values such as functions or Symbols.
- structuredClone
```js
const clone = obj => structuredClone(obj);
```

### **Concatenation of conditions vs includes**

When you find yourself in a case in which you have to test if a variable is x or y or more possibel values you may end with somethign like this:
```js
if ( a === 'value1' || a === 'value2' || a === 'value3') {
	console.log('yay!');
}
```
You can use `includes` instead!
```js
if ( ['value1', 'value2', 'value3'].includes(a) ) {
	console.log('yay!');
}
```


## **startsWith and endsWith**

When you are checking an error message or any type of string you can do it many ways. But people usally use includes or a regex. There are simpler solutions which not everybody knows. 

Let's imagine an error with the following format: `Checkout Not Found checkoutID: 267af68e-f74a-4aba-bcc8-432fdfb15d90`. How could we solve it?

- includes:
```js
if ( errorMessage.includes("Checkout Not Found checkoutID:") ) {
	console.log('Yes!');
}
```
In this case the string is so specific that maybe includes wont give you any problem but on smaller and less specific texts it can became a problem, you are checking if that string is any where in the source string.

- regex:
```js
if ( new RegExp('Checkout Not Found checkoutID: .*').test( errorMessage ) ) {
	console.log('Yes!');
}
```
You can check it also with a regex and this is not a bad solution it is in fact a good one but when we are checking just how an string starts or ends there's a simpler built in solution: startsWith/endsWith

- startsWith:
```js
if ( errorMessage.startsWith("Checkout Not Found checkoutID:") ) {
	console.log('Yes!');
}
```
This is the simpler way to test if an string start with some string. `endsWith` does the same thing but with the ending of the string.


## **End the if hell -> guard clause**

Have you seen something like the following code?

```js
if( condition1 ) {
	if (condition2) {
		console.log('do something');
	} else {
		console.log('dont');
		if ( condition3 ) {
			console.log('doing something else');
			if ( condition4 ) {
				console.log('finish');
			}
		}
	}
}
```
In the end when you have a lot of nested ifs it can become hard to understand whats going on. May I present you, the guard clause! It is just a way of structuring the if that makes the code easier to read. First let me show you the previous code with guard clauses:

```js
if( !condition1 ) return;
if ( condition2 ) return console.log('do something');

console.log('dont');
if ( !condition3 ) return;

console.log('doing something else');
if ( !condition4 ) return;

console.log('finish');
```
As you can see this method removes all the unnecesary nesting and makes the conditions easier to read one after another. The idea is to use a  negative condition and a return statement if the `if` was just to protect the code inside to be run, this means when we have an if without an else. And if we do have an else you use also an if but with the same condition and a return inside.

Remember to use this thoughtfully, you can't use a return if there's code after this in the same function. You can extract this into a function of course.

## **groupBy**

When you want to turn an array into an object based on a value the common way to go in the following:
```js
projects.reduce((result, project) => {
    if ( !result[project.status] ) {
	result[project.status] = [];
    }
    result[project.status].push(project)
    return result;
}, {})
```
Enven though we can simplyfy it with elements already explained here:
```js
projects.reduce((result, project) => {
    result[project.status] ??= [];
    result[project.status].push(project)
    return result;
}, {})
```
It not the best option, cause we have a built in function that achives exactly this more easily:
```js
Object.groupBy(projects, ({ status }) => status)
```

As you can see this built in function simplify the way of creating objects from an array of objects taking into a ccount a value. Of course you can use this also to group any way you image you have have to provide the function that returns the category of each object.

## **finally**

`.finally` is a promise callback which is not used that much. It is trigger after then or catch, it doesnt matter if the promise fails or not, this will be triggered anyways. Most of the use cases will be covered byt just using then or catch but this callback can be very usefull in some situations.

Example:
Image you you have to send a request to a server and in the meanwhile you have to display a loading icon. Let's use react for this example:

```js
const component = () => {
	const [loading, setLoading] = useState(false);

	useEffect(() => {
		(async () => {
			setLoading(true);
			const response = await fetch('/save/whatever');
			setLoading(false);
		})()
	}, [])

	// more code ...
}
```
I know this can be improved but just for the sake of learning let's focus on setLoading and what would happen if the request fails. What do you think?

Yeah if it fails setLoading would never be trigger and this component will be forever loading. We could add a catch to handle that and then use setloading but there a built in solution, `.finally`.

```js
const component = () => {
	const [loading, setLoading] = useState(false);

	useEffect(() => {
		(async () => {
			setLoading(true);
			const response = await fetch('/save/whatever').finally(() => setLoading(false));
		})()
	}, [])

	// more code ...
}
```
This will ensure that setLoading is always turned to false when the request is resolved or rejected and if you dont need a catch you dont need to write it.
