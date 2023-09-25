# Fun with loops and casinos...


In our daily job, we use two essential data structure in JS: 

- [Arrays](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Array)
- [Objects](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Object) 

and we iterate over them.

## Arrays <sub>[doc](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Array)</sub> and loops

- An array is a list of items and you access them by its position or index.
- The first position is located at index 0

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

### .at(idx) VS of [idx]

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

but it is too long for the same code (and some people does not understand those `&&`). Use `?` instead (this `?` is not the [ternary operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_operator)!)

### Loops
 
WIP
Hablar de performance VS legibilidad/fácil escritura (con este ejemplo: https://leanylabs.com/blog/js-forEach-map-reduce-vs-for-for_of)

### The triforce:

- map
- filter
- reduce

Otro que habla de performance, buen artículo: https://leanylabs.com/blog/js-forEach-map-reduce-vs-for-for_of/
Ejemplo map para mi: https://medium.com/@ExplosionPills/map-vs-for-loop-2b4ce659fb03
Performance: https://towardsdatascience.com/javascript-performance-test-for-vs-for-each-vs-map-reduce-filter-find-32c1113f19d7

## Objects <sub>[doc](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Object)</sub>

- We access an element of an object not per its position or index but per its key.

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
      ...product, //all the content (keys and values) of product
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
      ...product, //all the content (keys and values) of product
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
    ...product, //all the content (keys and values) of product
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
      ...product, //all the content (keys and values) of product
      availability: availability[product.id], //a new key/field "availability" with the availability data of this product
      price: price[product.id] //a new key/field "price" with the price data of this product
      }
  ); //push modifies the array

  return acc;
}, []); //note that we initialized with an empty array instead of an empty object
```

wait, wait, wait, it make sense to use a `.reduce` to return an array? 

Not in that case, we could simply use `.map` instead of  `.reduce`

```javascript
return Object.values(products).map(product => ({
    ...product, //all the content (keys and values) of product
    availability: availability[product.id], //a new key/field "availability" with the availability data of this product
    price: price[product.id] //a new key/field "price" with the price data of this product
  })
);
```

So, when do you want to use map and when reduce? It depends of course. Reduce is a powerful function but not everybody understands it. In the example, the best combination was using Object.values + a map over it to return a transformed array of items.



## Async and Arrays 

WIP

## Links of interest

- [MDN Object](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Object)
- [MDN Array](https://developer.mozilla.org/es/docs/Web/JavaScript/Reference/Global_Objects/Array)
- [5 array methods you should use today](https://colintoh.com/blog/5-array-methods-that-you-should-use-today?utm_source=javascriptweekly&utm_medium=email)
- [From map/reduce to JS functional programming](https://hacks.mozilla.org/2015/01/from-mapreduce-to-javascript-functional-programming/)
- Bizarre Bonus track: when you try filter/map/reduce and came back to a previous language without this functions, you will miss them... [Map, filter, reduce and flatMap implementations for NSArray (Objective-C)](https://betterprogramming.pub/higher-order-functions-in-objective-c-850f6c90de30)