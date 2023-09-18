# Destructuring is your friend

[MDN destructuring assignment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) info

## Key points

- you could destructure an object
- you could destructure an array
- bonus track: you could also destructure strings

## Objects

Let's play to get some specific properties from an object
```javascript
const z = { a: 1, b: 2, c: 3, d: 4, e: 5, f: 6, g: 7 };

const { a, b } = z;
console.log(a); //1
console.log(b); //2
```

This is equivalent to:
```javascript
const z = { a: 1, b: 2, c: 3, d: 4, e: 5, f: 6, g: 7 };

const a = z.a;
const b = z.b;

console.log(a); //1
console.log(b); //2
```

Let's remove "a", "b" properties from an object:
```javascript
const z = { a: 1, b: 2, c: 3, d: 4, e: 5, f: 6, g: 7 };

const { a: _a, b: _b, ...rest } = z; //we select a and put it into _a variable, select b and put it into _b variable

console.log(rest); //{ c: 3, d: 4, e: 5, f: 6, g: 7 }
```

Let's add a new property into an object

```javascript
const item = {
  id: '90466155',
  name: 'Glis',
  image: 'https://www.ikea.com/es/es/images/products/glis-caja-con-tapa-amarillo-azul__0795103_pe765864_s5.jpg'
};

const price = {
  normal: 2.49,
  currency: 'EUR',
  familyPrice: 2
};

const addPriceToProduct = (product, price) => ({ ...product, price});

const itemWithPrice = addPriceToProduct(item, price);
console.log(itemWithPrice);

// {
//   id: '90466155',
//   name: 'Glis',
//   image: 'https://www.ikea.com/es/es/images/products/glis-caja-con-tapa-amarillo-azul__0795103_pe765864_s5.jpg',
//   price: { normal: 2.49, currency: 'EUR', familyPrice: 2 }
// }
```

Remember:

```javascript
const addPriceToProduct = (product, price) => ({ ...product, price});
```

is equivalent to:
```javascript
const addPriceToProduct = (product, price) => {
  const newProduct = Object.assign({}, product);
  newProduct.price = price;

  return price;
}
```

:warning: it is a best practice to not modify an input parameter in a function. It is much better to return a modified copy of it.

Please, do not do that:

```javascript
const addPriceToProduct = (product, price) => {
  product.price = price;

  return product;
}
```
### Real life examples

#### Send function parameters as objects

When you create a function, it is more convenient to send the parameters as an object and not as an argument. Why? 
Sometimes you need to propagate the parameters to other functions and you don't want to add this one per one propagation in all the functions and you are sure you won't mess with the order of the parameters.

When destructuring parameters, it is good to put {} as default value. Let's see why:

```javascript
const a = ({retailUnit, language}) => {
  console.log(retailUnit, language);
}

a();
> Uncaught:
> TypeError: Cannot destructure property 'retailUnit' of 'undefined' as it is undefined.

```

with default value {}:
```javascript
const a = ({retailUnit, language} = {}) => {
  console.log(retailUnit, language);
}

a();
> undefined undefined
```

It could destructure the object {} but as it has no retailUnit and language property inside, the values for those variables are both undefined

This is a controller that recabs all the parameters and send it to a datasource. The datasource will call different providers and then mix the information.


```javascript
//controller
const ctrl = async (req, res, next) => {
  const { retailUnit, language, ids, postalCode, store, transactionId } = req.b2bHelper();

  try{
    //postalCode and store are optionals
    const options = { retailUnit, language, id, postalCode, store, transactionId };

    const result = await datasource.get(options);

    return res.send(result);
  } catch(error){
    return next(error);
  }
}

//datasource
const { range, prices, availability} = require('./providers');
const { join } = require('./parsers');

module.exports.get = options => {
  return Promise.all([range.get(options), prices.get(options), availability.get(options)]).then(join);  
}

//range provider
const { factory, queryHelper } = require('@b2b/fetch');
const { api } = require('../options');
const fetch = factory(api.range);


module.exports.get = async ({ id, retailUnit, language, transactionId } = {}) => {
  const url = `/v1/${retailUnit}/${language}/product/${id}`;
  const options = { transactionId };

  return fetch(url, options);
};

//price provider
const { factory, queryHelper } = require('@b2b/fetch');
const { api } = require('../options');
const fetch = factory(api.price);


module.exports.get = async ({ id, retailUnit, transactionId } = {}) => {
  const url = `/v1/${retailUnit}}/price/${id}`;
  const options = { transactionId };

  return fetch(url, options);
};

//availability provider
const { factory, queryHelper } = require('@b2b/fetch');
const { api } = require('../options');
const fetch = factory(api.price);


module.exports.get = async ({ id, retailUnit, postalCode, store, transactionId } = {}) => {
  const params = queryHelper({ postalCode, store }); //we use this helper to ignore undefined values to avoid creating queries like ?store=undefined&postalCode=undefined
  const url = `/v1/${retailUnit}}/availability/${id}?${params}`;
  const options = { transactionId };

  return fetch(url, options);
};
```

## Array

For the arrays, it is all about of index position.

```javascript
const a = [0, 1, 2, 3, 4, 5, 6];
```

you could destructure like:

```javascript
const [firstValue, secondValue, ...otherValues] = a;

console.log(firstValue); //0
console.log(secondValue); //1
console.log(otherValues); //[ 2, 3, 4, 5, 6 ]
```

```javascript
const [firstValue, , thirdValue] = a;

console.log(firstValue); //0
console.log(thirdValue); //2
```

You could assign default values...
```javascript
const a = [undefined, 1, 2, 3, 4, 5, 6]

const [first, ...oth] = a
console.log(a) //[ undefined, 1, 2, 3, 4, 5, 6 ]
console.log(first) //undefined
console.log(oth) //[ 1, 2, 3, 4, 5, 6 ]

//let's assign a default value for first...
const a = [undefined, 1, 2, 3, 4, 5, 6]

const [first = 0, ...oth] = a
console.log(first); //0
console.log(oth); //[ 1, 2, 3, 4, 5, 6 ]
```

And maybe you want to remove the first two values of the array:

```javascript
const a = [ 0, 1, 2, 3, 4, 5, 6 ];

const [_f, _s, ...oth] = a;

return oth; //[ 2, 3, 4, 5, 6 ]
```

### Real life examples

#### Creating an array with non repeated ids [with Set](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Set)

```javascript
const a = [ 0, 1, 0, 2, 1, 3, 4, 3, 2, 6, 7, 0, 3, 8];

const nonRepeated = [... new Set(a)];
console.log(nonRepeated) //[ 0, 1, 2, 3, 4, 6, 7, 8]
```

#### Promise.all
Assigning to named variables the results of a Promise.all:

```javascript
const [detail, availability, price] = await Promise.all([
  fetch(`/products/${id}`),
  fetch(`/availabilities/${id}`),
  fetch(`/prices/${id}`)
]);

return { ...detail, availability, price}; //we return the item detail adding it two new properties to the detail: availability and price
```

Promise.all accepts an array of promises. The results, are stored into another array respecting the position of the promise:

- promise 1 (fetch products) was at index 0, the response of promise 1 will be at index 0
- promise 2 (fetch availabilities) was at index 1, the response of promise 2 will be at index 1
- promise 3 (fetch prices) was at index 2, the response of promise 3 will be at index 2

#### React Example

In react, when you use [state hook](https://react.dev/learn/typescript#typing-usestate) you are doing a destructuring assignment:

```javascript
import React, { useState } from 'react';

const [enabled, setEnabled] = useState(false);
```

useState in fact is returning an array of (at least) two values:
- the first one is the getter function
- the second one is the setter function

by returning it as an array, you could yse destructuring assignment to rename these two functions with the name you really need.

#### :warning: HACK! Function arguments 

Function arguments are in fact an array of arguments. Let's create a decorator of a function to check how long it takes to be executed.

```javascript
const { performance } = require('perf_hooks');

const decorate = fn => {
  
  return (...args) => {
    const startedOn = performance.now();
    const result = fn(...args);
    const duration = performance.now() - startedOn;
    console.log(`duration ${duration}ms`);

    return result;
  }
}
```

We created a generic decorator so it will accept functions with different parameters.

Example:

```javascript
const { performance } = require('perf_hooks');

const decorate = fn => {
  
  return (...args) => {
    const startedOn = performance.now();
    const result = fn(...args);
    const duration = performance.now() - startedOn;
    console.log(`duration ${duration}ms`);

    return result;
  }
}

const add = (a, b) => a + b;
const decoratedAdd = decorate(add);
console.log(decoratedAdd(3,4)); //duration 0.013687968254089355ms
                                //3
```

Let's create a function that will sum its parameters, but not limiting to only 2 numbers:

```javascript
const multiAdd = (...args) => args.reduce((acc, it) => acc + it, 0);

console.log(multiAdd(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)); //55
```

## Bonus track: strings

Destructuring and string as an array:

```javascript
const a = 'Neque porro quisquam est qui dolorem ipsum quia dolor sit amet, consectetur, adipisci velit';
const b = [ ...a ];

// [
//   'N', 'e', 'q', 'u', 'e', ' ', 'p', 'o', 'r', 'r', 'o',
//   ' ', 'q', 'u', 'i', 's', 'q', 'u', 'a', 'm', ' ', 'e',
//   's', 't', ' ', 'q', 'u', 'i', ' ', 'd', 'o', 'l', 'o',
//   'r', 'e', 'm', ' ', 'i', 'p', 's', 'u', 'm', ' ', 'q',
//   'u', 'i', 'a', ' ', 'd', 'o', 'l', 'o', 'r', ' ', 's',
//   'i', 't', ' ', 'a', 'm', 'e', 't', ',', ' ', 'c', 'o',
//   'n', 's', 'e', 'c', 't', 'e', 't', 'u', 'r', ',', ' ',
//   'a', 'd', 'i', 'p', 'i', 's', 'c', 'i', ' ', 'v', 'e',
//   'l', 'i', 't'
// ]
```

So, when you unexpectedly received this instead of an string, be sure you are destructuring a string by error.