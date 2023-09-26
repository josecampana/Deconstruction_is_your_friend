# Fun with loops and casinos...

In our daily job, we use two essential data structure in JS: [Arrays](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Array) and [Objects](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Object) and we iterate over them.

## Index of content

- [Arrays and loops](#arrays-and-loops)
  - [Check if an array is empty](#checking-if-array-is-empty)
  - [Accessing elements of an array](#accessing-by-index-atidx-vs-of-idx)
  - [Loops](#loops)
  - **The triforce**
    - [map](#map)
    - [filter](#filter)
    - [reduce](#reduce)
      - [array VS number](#example-1-reduce-that-will-return-a-number)
      - [array VS boolean](#example-2-reduce-that-will-return-a-boolean)
      - [array VS string](#example-3-reduce-that-will-return-a-string)
      - **[array VS object](#example-4-reduce-that-will-return-an-object)**
- [Objects](#objects)
  - [keys, values, loops and transformations](#keys-values-loops-and-transformations)

## Arrays and loops

- An array is a list of items and you access them by its position or index.
- The **first element** is located at **index 0**

You could access an element by doing:

```javascript
> const list = ['a', 'b', 'c', 'd', 'e'];

> list[0]
'a'
> list.at(0)
'a'
> list[8]
undefined
> list.at(8)
undefined
> list.length
5
> list[list.length]
undefined //because it starts at 0 so last element is at (list.length - 1)
> list[list.length-1]
'e'

//you could get a portion of the array
> list.slice(0,3)
[ 'a', 'b', 'c' ]
```

### Checking if array is empty

Let's check if an array has values...

```javascript
> const list = ['a', 'b', 'c', 'd', 'e'];
> list.length
5
> list.length > 0
true //has elements
> Boolean(list.length)
true //has elements

> const empty = []
undefined
> empty.length
0
> empty.length > 0
false //is empty
> Boolean(empty.length)
false //is empty
```

So you could create this function using the javascript [falsy](https://developer.mozilla.org/en-US/docs/Glossary/Falsy):
```javascript
const isEmpty = list => !Boolean(list?.length)

> isEmpty([1,2,3,4])
false
> isEmpty([])
true
> isEmpty()
true
``` 

Note that `list?.length` is crutial to avoid crashes on cases like `isEmpty()`. Let's check the difference:

```javascript
const isEmpty = list => !Boolean(list.length)

> isEmpty([1,2,3,4])
false
> isEmpty([])
true
> isEmpty()
Uncaught TypeError: Cannot read properties of undefined (reading 'length')
    at isEmpty (REPL1:1:39)
``` 

You could also create this version:

```javascript
const isEmpty = list => !(list && list.length > 0);

> isEmpty([1,2,3,4])
false
> isEmpty([])
true
> isEmpty()
true
```

or this one if you don't like to negate logic:

```javascript
const isEmpty = list => !list || list.length === 0;

> isEmpty([1,2,3,4])
false
> isEmpty([])
true
> isEmpty()
true
```

**I prefeer this version (more elegant)**: `const isEmpty = list => !Boolean(list?.length);`

![](./img/elegant.jpg)

### accessing by index: .at(idx) VS of [idx]

```javascript
const product = {
  id: '00263850',
  name: 'Billy',
  description: 'Librería, blanco, 80x28x202 cm',
  image: {
    'altText': 'BILLY Librería, blanco, 80x28x202 cm',
    'S1': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s1.jpg',
    'S2': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s2.jpg',
    'S3': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s3.jpg',
    'S4': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s4.jpg',
    'S5': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s5.jpg'
  },
  variants: [
    '90404209'
  ]
}
```

Let's access the variants of this product:

```javascript
> product.variants[0]
'90404209'
> product.variants[1]
undefined
```

We could access them without project. Now let's check another product without variants but using "the same code" to access them:

```javascript
const product2 = {
  id: '00263850',
  name: 'Billy',
  description: 'Librería, blanco, 80x28x202 cm',
  image: {
    'altText': 'BILLY Librería, blanco, 80x28x202 cm',
    'S1': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s1.jpg',
    'S2': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s2.jpg',
    'S3': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s3.jpg',
    'S4': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s4.jpg',
    'S5': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s5.jpg'
  }
}
```

It has no `variants` key (it is not an empty array, is undefined). Let's repeat the previous code:

```javascript
> product2.variants[0]
Uncaught TypeError: Cannot read properties of undefined (reading '0')

> product2?.variants[0]
Uncaught TypeError: Cannot read properties of undefined (reading '0')
```

It could be saved with:

```javascript
> product2?.variants?.at(0)
undefined

> product?.variants?.at(0)
'90404209'
```

we could also do:
```javascript
> product2 && product2.variants && product2.variants[0]
undefined
```

but it is too long for the same code (and some people does not understand those `&&`). Use `?` instead (this `?` is not the [ternary operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_operator)!). _ELEGAAAANT..._

### Loops

In terms of performance, the _for_ loop is the winner when we talk about javascript. But it will depend of course in the operations we want to perform over it.

But you do not need to obsess about performance, it is better to be more obsess about readability. I guess that talking about reduce an readability could look a contradiction for some people (joke).



Let's see an example: we have a list of items, we want to generate an id on them and return it into a new array.

```javascript
const addId = list => {
  for(let idx = 0; idx < list.length; idx++){
    list[idx].id = idx;
  }

  return list;
}

const a = [{name: 'zero'}, { name: 'one'}, { name: 'two'}];
const b = addId(a);

> a
[
  { name: 'zero', id: 0 },
  { name: 'one', id: 1 },
  { name: 'two', id: 2 }
]
> b
[
  { name: 'zero', id: 0 },
  { name: 'one', id: 1 },
  { name: 'two', id: 2 }
]
```

Ups... _a_ and _b_ are the same... we do not want to modify _a_...

What happened is that, when passing an array as an argument of a function, we passed it as a reference (a memory link to the variable) because to send a full copy of an array could decrease a lot the performance...

In fact, we could just return nothing in the _addId_ function and we will get the same result:

```javascript
const addId = list => {
  for(let idx = 0; idx < list.length; idx++){
    list[idx].id = idx;
  }
}

const a = [{name: 'zero'}, { name: 'one'}, { name: 'two'}];
addId(a);

> a
[
  { name: 'zero', id: 0 },
  { name: 'one', id: 1 },
  { name: 'two', id: 2 }
]
```

We do not need to return anything because we modified the original one...

:warning: **as a best practice**, we try to not modify the original array (or object) inside of a function, so we use to generate a new copy with the modifications. That´s because `.map()` is too useful/popular (and the [destructuring assignment](./destructuring.md))

So, let's create a copy of the array into the _addId_ function:

```javascript
const addId = list => {
  let newList = [];

  for(let idx = 0; idx < list.length; idx++){
    newList.push({...list[idx], id: idx});
  }

  return newList;
}

const a = [{name: 'zero'}, { name: 'one'}, { name: 'two'}];
const b = addId(a);

> a
[ { name: 'zero' }, { name: 'one' }, { name: 'two' } ]
> b
[
  { name: 'zero', id: 0 },
  { name: 'one', id: 1 },
  { name: 'two', id: 2 }
]
```

Let's create a more short version of this code by using `.map()`:

```javascript
const addId = list => list.map((item, idx) => ({...item, id: idx}));
const a = [{name: 'zero'}, { name: 'one'}, { name: 'two'}];
const b = addId(a);

> a
[ { name: 'zero' }, { name: 'one' }, { name: 'two' } ]
> b
[
  { name: 'zero', id: 0 },
  { name: 'one', id: 1 },
  { name: 'two', id: 2 }
]
```

![](./img/elegant.jpg)

So, do not obsess with performance until it is really needed (and that could come in a future iteration of your code).

For "normal" loops, take a look at the [MDN loop reference](https://developer.mozilla.org/es/docs/Web/JavaScript/Guide/Loops_and_iteration). Here, we are going to talk about the array's **triforce**:




#### .map()
A member of the triforce. It iterates over an array and return a new array array with the same number of items as the original. 

![](./img/triforce.png)

_.map()_ has an argument: a function that will receive the current item as argument and must return an item for the current index of the new array.

Example: create a function that sums 1 to all the items of an integer's array.

```javascript
const a = [ 0, 1, 2, 3, 4, 5 ];

const addOne = item => item + 1;

const b = a.map(addOne);

> a
[ 0, 1, 2, 3, 4, 5 ]
> b
[ 1, 2, 3, 4, 5, 6 ]
```

Remember that maps generates a new array, it does not modify the original one.

We could write it in a more compact way because this map function is quite small:

```javascript
const a = [ 0, 1, 2, 3, 4, 5 ];
const b = a.map(item => item + 1);

> a
[ 0, 1, 2, 3, 4, 5 ]
> b
[ 1, 2, 3, 4, 5, 6 ]
```

This example is quite simple, let's _complicate_ it a bit more: we want to add a number (not just 1) to all the  elements of the list.

```javascript
const a = [ 0, 1, 2, 3, 4, 5 ];

const addOne = number => item => item + number;


const b = a.map(addOne(7));

> a
[ 0, 1, 2, 3, 4, 5 ]
> b
[ 7, 8, 9, 10, 11, 12 ]
```

We take profit that a function could return another function to inject the number we want to sum. We will check this more in depth in [Tweaks and Tricks](./tweaks_and_tricks.md).

If you are not use to ES6, maybe this syntax will helps you:

```javascript
const a = [ 0, 1, 2, 3, 4, 5 ];

function addOne (number){
  return function (item) {
    return item + number;
  }
}

const b = a.map(addOne(7));

> a
[ 0, 1, 2, 3, 4, 5 ]
> b
[ 7, 8, 9, 10, 11, 12 ]
```

If this is your case, please do an effort to upgrade your JS syntax:

```javascript
function addOne (number){
  return function (item) {
    return item + number;
  }
}
```

VS

`const addOne = number => item => item + number;`

##### Promises executed in parallel

_.map()_ does a great job when executing promises in parallel with **Promise.all**


#### .filter()
A member of the triforce. It iterates over an array and let pass only those that meet a condition. The result will be a new array with max, the same number of elements than the original one.

![](./img/triforce.png)

_.filter()_ has an argument: a function that will receive the current item as argument and must return true or false depending if the item acomplish some conditions.

Examples:

```javascript
const a = [ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15 ];
const isOdd = n => n % 2 === 1; 
const isPair = n => n % 2 === 0;

const oddNumbers = a.filter(isOdd);
const pairNumbers = a.filter(isPair);

> a.length
16
> oddNumbers.length
8
> pairNumbers.length
8

> oddNumbers
[
  1,  3,  5,  7,
  9, 11, 13, 15
]
> pairNumbers
[
  0,  2,  4,  6,
  8, 10, 12, 14
]

//let's play with one of this filter functions we used:
> a.map(isOdd)
[
  false, true,  false,
  true,  false, true,
  false, true,  false,
  true,  false, true,
  false, true,  false,
  true
]
```

_%_ is the javascript [remainder operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_operators#arithmetic_operators), the remainder when you divide two numbers


#### .reduce()
A member of the triforce. It iterates over an array and will **accumulate** the values into the same or a different structure: array, object, number, boolean, string...
![](./img/triforce.png).

.reduce() has two arguments: 

- a function that will receive the current accumulate of previous operations and the item to evaluate. It must return the new accumulation of operations including current item.
- the initial value of the accumulate.

So, _reduce_ is a great operation to do array transformations (that won't result into a new array).

##### example 1: reduce that will return a number

We want to sum all the numbers of a list `[ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15 ]`

First approach:

```javascript
const a = [ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15 ];
let result = 0;

for(n in a){
  result+=n;
}

> result
'00123456789101112131415'
```
unexpected result! Let's cast the numbers to force to be integer...

```javascript
const a = [ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15 ];
let result = 0;

for(n in a){
  result+=parseInt(n);
}

> result
120
```

Ok, let's change the and use a normal loop accessing to the item to avoid the casting...

```javascript
const a = [ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15 ];
let result = 0;

for(let i=0; i<a.length; i++){
  result += a[i]
}

> result
120
```

We are accumulating into result the sum of the previous numbers, starting with the initial value 0. It sounds like a reduce:

```javascript
const a = [ 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15 ];
const result = a.reduce((acc, n) => acc + n, 0);

> result
120
```

##### example 2: reduce that will return a boolean

We want to know if all the elements of a list are true.

###### For way
```javascript
const a = [true, true, true, true, true, true, false, true];
const b = [true, true, true, true];

let allTrueA = true;

for(let idx = 0; idx < a.length; idx++){
  allTrueA &&=a[idx]
}

let allTrueB = true;
for(let idx = 0; idx < b.length; idx++){
  allTrueB &&=b[idx]
}

> allTrueA
false
> allTrueB
true
```

Let's tune this soluton...

```javascript
const a = [true, true, true, true, true, true, false, true];
const b = [true, true, true, true];

const allTrue = list => {
  let result = true;

  for(let idx = 0; idx < list.length; idx++){
    result &&=list[idx]
  }

  return result;
}

const allTrueA = allTrue(a);
const allTrueB = allTrue(b);

> allTrueA
false
> allTrueB
true

```


##### Reduce way
```javascript
const a = [true, true, true, true, true, true, false, true];
const b = [true, true, true, true];

const allTrue = (acc, item) => acc &&=item;

const allTrueA = a.reduce(allTrue, true);
const allTrueB = b.reduce(allTrue, true);

> allTrueA
false
> allTrueB
true
```

##### The best way

Check for _[.every()](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Array/every)_ and _[.some()](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Array/some)_ functions

 ```javascript
const a = [true, true, true, true, true, true, false, true];
const b = [true, true, true, true];

const allTrueA = a.every(n => n);
const allTrueB = b.every(n => n);

> allTrueA
false
> allTrueB
true
``````

##### example 3: reduce that will return a string

We have a list of words and we want to create a phase with them. Check the [Chiquito Ipsum](https://www.chiquitoipsum.com/) for a different Lorem Ipsum generator.

###### For way
```javascript
const words = ['Lorem', 'fistrum', 'mamaar', 'apetecan', 'hasta', 'luego', 'Lucas', 'de', 'la', 'pradera.'];

let phrase = '';

for(let idx = 0; idx < words.length; idx++){
  phrase +=`${words[idx]} `;
}

> phrase
'Lorem fistrum mamaar apetecan hasta luego Lucas de la pradera. '
```

###### Reduce way
```javascript
const words = ['Lorem', 'fistrum', 'mamaar', 'apetecan', 'hasta', 'luego', 'Lucas', 'de', 'la', 'pradera.'];

const phrase = words.reduce((acc, word) => phrase +=`${word} `, '');

> phrase
'Lorem fistrum mamaar apetecan hasta luego Lucas de la pradera. '
```

Both solutions are not perfect: they put a blank space at the end of the phrase. It could be fixed easily with a `.trim()` but let's check a best solution.

###### The best way

```javascript
const words = ['Lorem', 'fistrum', 'mamaar', 'apetecan', 'hasta', 'luego', 'Lucas', 'de', 'la', 'pradera.'];
const phrase = words.join(' ');

> phrase
'Lorem fistrum mamaar apetecan hasta luego Lucas de la pradera.'
```

##### example 4: reduce that will return an object


Otro que habla de performance, buen artículo: https://leanylabs.com/blog/js-forEach-map-reduce-vs-for-for_of/
Ejemplo map para mi: https://medium.com/@ExplosionPills/map-vs-for-loop-2b4ce659fb03
Performance: https://towardsdatascience.com/javascript-performance-test-for-vs-for-each-vs-map-reduce-filter-find-32c1113f19d7

## Objects

- We access an element of an object not by its position or index but by its key.

```javascript
const product = {
  id: '00263850',
  name: 'Billy',
  description: 'Librería, blanco, 80x28x202 cm',
  image: {
    'altText': 'BILLY Librería, blanco, 80x28x202 cm',
    'S1': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s1.jpg',
    'S2': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s2.jpg',
    'S3': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s3.jpg',
    'S4': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s4.jpg',
    'S5': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s5.jpg'
  }
}
```

```bash
> product.id
'00263850'
> product.name
'Billy'
> product.image.S1
'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s1.jpg'
> product.image.S6
undefined
> product.fistro
undefined
> product.image.s1
Uncaught TypeError: Cannot read properties of undefined (reading 's1')
```

Yep, we tried to access to product.image.s1 without checking that this key exists (exists S1 but not s1 because keys are **case sensitive**.

Avoid it with

```bash
> product?.image?.s1
undefined
```

### keys, values, loops and transformations

Let's guess that an API returns as a list of products like this:

```javascript
const items = [{
  id: '00263850',
  name: 'Billy',
  description: 'Librería, blanco, 80x28x202 cm',
  image: {
    'altText': 'BILLY Librería, blanco, 80x28x202 cm',
    'S1': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s1.jpg',
    'S2': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s2.jpg',
    'S3': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s3.jpg',
    'S4': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s4.jpg',
    'S5': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s5.jpg'
  },
  {
    id: '90404209',
    name: 'Billy',
    description: 'Librería, chapa roble tinte blanco, 80x28x202 cm'
    image: {
      'altText': 'BILLY Librería, chapa roble tinte blanco, 80x28x202 cm',
      'S1': 'https://www.ikea.com/es/es/images/products/billy-libreria-chapa-roble-tinte-blanco__0564818_pe664196_s1.jpg',
      'S2': 'https://www.ikea.com/es/es/images/products/billy-libreria-chapa-roble-tinte-blanco__0564818_pe664196_s2.jpg',
      'S3': 'https://www.ikea.com/es/es/images/products/billy-libreria-chapa-roble-tinte-blanco__0564818_pe664196_s3.jpg',
      'S4': 'https://www.ikea.com/es/es/images/products/billy-libreria-chapa-roble-tinte-blanco__0564818_pe664196_s4.jpg',
      'S5': 'https://www.ikea.com/es/es/images/products/billy-libreria-chapa-roble-tinte-blanco__0564818_pe664196_s5.jpg'
    }
  }
}]
```

That does not means that you need to work with this structure, you could transform it into a more convenient data structure to access its elements like objects (in this example, we want to access the elements by its product id).

We want to get something like:

```javascript
const items = {
  '00263850': {
    id: '00263850',
    name: 'Billy',
    description: 'Librería, blanco, 80x28x202 cm',
    image: {
      'altText': 'BILLY Librería, blanco, 80x28x202 cm',
      'S1': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s1.jpg',
      'S2': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s2.jpg',
      'S3': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s3.jpg',
      'S4': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s4.jpg',
      'S5': 'https://www.ikea.com/es/es/images/products/billy-libreria-blanco__0625599_pe692385_s5.jpg'
    }
  },
  {
    '90404209': {
      id: '90404209',
      name: 'Billy',
      description: 'Librería, chapa roble tinte blanco, 80x28x202 cm'
      image: {
        'altText': 'BILLY Librería, chapa roble tinte blanco, 80x28x202 cm',
        'S1': 'https://www.ikea.com/es/es/images/products/billy-libreria-chapa-roble-tinte-blanco__0564818_pe664196_s1.jpg',
        'S2': 'https://www.ikea.com/es/es/images/products/billy-libreria-chapa-roble-tinte-blanco__0564818_pe664196_s2.jpg',
        'S3': 'https://www.ikea.com/es/es/images/products/billy-libreria-chapa-roble-tinte-blanco__0564818_pe664196_s3.jpg',
        'S4': 'https://www.ikea.com/es/es/images/products/billy-libreria-chapa-roble-tinte-blanco__0564818_pe664196_s4.jpg',
        'S5': 'https://www.ikea.com/es/es/images/products/billy-libreria-chapa-roble-tinte-blanco__0564818_pe664196_s5.jpg'
      }
    }
  }
}
```

In that case we can access to a product directly by its id, inject more data (for example availability and price, data), and return it.

```javascript
const products = items.reduce((acc, product) => {
  acc[product.id] = product;

  return acc;
}, {});
```
Note: `acc` is the accumulate result of the loop.

Now, we have the list of products in an object format to access the elements by the product id. (Of course you could try to do it by using `.find()` in the array but you are going to iterate the array everytime you want to access an element).

Let's guess that we receive availability and price data from APIs in that format (based on real facts):

```javascript
const availability = {
  '00263850': {
    delivery: 10,
    clickNCollect: 4
  },
  '90404209': {
    delivery: 20,
    clickNCollect: 9
  }
};

const price = {
  '00263850': {
    amount: 59.0,
    currency: 'EUR'
  },
  '90404209': {
    amount: 89.0,
    currency: 'EUR'
  }
}
```

So, let's inject availability and price data into products:

```javascript
return Object.keys(products).reduce((acc, key) => {
  const product = products[key];

  return { 
    ...acc, 
    [key]: {
      ...product, //a copy of all the product content
      availability: availability[key], //a new key/field "availability" with the availability data of this product
      price: price[key] //a new key/field "price" with the price data of this product
      }
    };
}, {});
```

What we are doing is: 

- Getting all the keys of `products` object and do a loop over them. 
- This loop, will generate a new object so we initialize it with an empty object `{}`.
- On each iteration (we are doing a loop over the keys of the `products` object)
- We return the previous items (the accumulation of other iterations) + a new key with the product data + the availability and price for this product id (the key)

As each product has the key `id`, we can do it in another way:

```javascript
return Object.values(products).reduce((acc, product) => {
  const id = product.id;

  return { 
    ...acc, 
    [id]: {
      ...product, //a copy of all the product content
      availability: availability[id], //a new key/field "availability" with the availability data of this product
      price: price[id] //a new key/field "price" with the price data of this product
      }
    };
}, {});
```

`Object.values` returns the values of the keys, not the keys itself.

I think I can save some lines of code...

```javascript
return Object.values(products).reduce((acc, product) => ({
  ...acc, 
  [product.id]: {
    ...product, //a copy of all the product content
    availability: availability[product.id], //a new key/field "availability" with the availability data of this product
    price: price[product.id] //a new key/field "price" with the price data of this product
  }
}), {});
```

Ok, ok, but I need to return it into an array format...

```javascript
return Object.values(products).reduce((acc, product) => {
  acc.push(
    {
      ...product, //a copy of all the product content
      availability: availability[product.id], //a new key/field "availability" with the availability data of this product
      price: price[product.id] //a new key/field "price" with the price data of this product
      }
  ); //push modifies the array

  return acc;
}, []); //note that we initialized with an empty array instead of an empty object
```

In this case, we could simply use `.map` instead of  `.reduce` (it has no sense because we have a better solution)

```javascript
return Object.values(products).map(product => ({
    ...product, //a copy of all the product content
    availability: availability[product.id], //a new key/field "availability" with the availability data of this product
    price: price[product.id] //a new key/field "price" with the price data of this product
  })
);
```

![](./img/elegant.jpg)

### In a few words...

**When** I must to use **map VS reduce**? **It depends** of course. 

Reduce is a powerful function that allows you to transform an array into another accumulated type (an object, a boolean, an integer, another array, a string, ...) but not everybody understands it. 

In the example, the best combination was using `Object.values()` + `.map()` over it to return an array from a products object.



## Async and Arrays 

WIP

## Links of interest

- [MDN Object](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Object)
- [MDN Array](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Array)
- [5 array methods you should use today](https://colintoh.com/blog/5-array-methods-that-you-should-use-today?utm_source=javascriptweekly&utm_medium=email)
- [MDN JS loops reference](https://developer.mozilla.org/es/docs/Web/JavaScript/Guide/Loops_and_iteration)
- [From map/reduce to JS functional programming](https://hacks.mozilla.org/2015/01/from-mapreduce-to-javascript-functional-programming/)
- Bizarre Bonus track: when you try filter/map/reduce and came back to a previous language without this functions, you will miss them... [Map, filter, reduce and flatMap implementations for NSArray (Objective-C)](https://betterprogramming.pub/higher-order-functions-in-objective-c-850f6c90de30)

## Disclaimer

The author of this documentation has nothing to do with _Spy x Family_, _Futurama_ or _ゼルダの伝説_.