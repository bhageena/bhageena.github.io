---
layout: post
title: Essential Studies in ES8
description: "Essential Studies in ES8"
categories: blog
comments: true
tags: "ES8 JavaScript NewReleases Improvements"
excerpt: ""
share: true
readtime: true
---
### ES8 (ECMAScript 2017)

ECMAScript 2017, 8th edition (ES2017/ES8) has been released in June 2017. Since the standard is evolving as open source project, the change requests are actively being made, monitored and adopted by the community as proposals in ES.Next (a dynamic name that refers to whatever the next version is at time of writing). All of the changes in ES8 are available with Node 8 and with the latest version of browsers without babel or any polyfills. Let’s figure out some important changes...


* [Trailing Commas in function parameters](#trailing-commas)
* [Object.values / Object.entries](#objectvalues--objectentries)
    * [Object.values](#objectvalues)
    * [Object.entries](#objectentries)
* [Object.getOwnPropertyDescriptors](#objectgetownpropertydescriptors)
* [String Padding](#string-padding)
* [async / await](#async--await)
     * [Classes](#classes)
     * [Multiple Promises](#multiple-promises)
     * [Await and Thenable](#await-and-thenable)
* [Shared memory and atomics](#shared-memory-and-atomics)
<!-- * TOC
{:toc} -->


## Trailing Commas

`Trailing commas`, or sometimes referred as `final commas`, allow us to **add a comma to the last parameter** on a function declaration and execution.
This is specially useful when declaring or calling a function by placing each parameter per line.


In Function Declaration:
```js
function TrailEnd(
    a,
    b,
    c,  // <- comma
) {}
```
In Function Execution:
```js
TrailEnd(
    1,
    2,
    3,  // <- comma
)
```
In fact, in objects and arrays, trailing commas were already supported but with ES8, they are also part of the function parameters.
```js
// Previously implemented legal trailing commas for objects and arrays
var person = {
  name: "John Doe",
  age: 23,
};
var arr = [1,2,3,]  // you can use more than one comma at the end, it will add to the length
```

## Object.values / Object.entries

### Object.values

`Object.values` returns all values of an object in the same order as the `for...in` loop.

```js
var person =  {
    name: 'John Doe',
    age: 23
}

Object.values(person)  // ["John Doe", 23]
```

### Object.entries

`Object.entries` returns a pair **[key , value]** for each element in the object.

```js
JSON.stringify(Object.entries(person)) // [["name","John Doe"],["age",23]]
```

## Object.getOwnPropertyDescriptors

`Object.getOwnPropertyDescriptors` returns all object properties descriptors.

```js
JSON.stringify(Object.getOwnPropertyDescriptors(student))

/* {
"name": {
"value": "John Doe",
"writable": true,
"enumerable": true,
"configurable": true
},
"age": {
"value": 23,
"writable": true,
"enumerable": true,
"configurable": true
}
} */
```

## String Padding

`String padding` pads the current string with the provided string until it reaches the specified length. No matter how small a feature it seem but it once sent everyone in [tizzy](http://blog.npmjs.org/post/141577284765/kik-left-pad-and-npm). Now it's good to have it here.

padStart pads a string from the start until the given length is reached.
```js
// String.prototype.padStart
'stuffme'.padStart(15)              // "        stuffme"
'stuffme'.padStart(15,'feedme')     // "feedmefestuffme"
'stuffme'.padStart(15, undefined)   // "        stuffme"
```

padEnd pads a string from the end until the given length is reached.
```js
// String.prototype.padEnd
'stuffme'.padEnd(15)              // "stuffme        "
'stuffme'.padEnd(15,'feedme')     // "stuffmefeedmefe"
'stuffme'.padEnd(15, undefined)   // "stuffme        "
```

## async / await

`async / await` is the new Javascript syntax to declare an `asynchronous function`. It's a new way to write asynchronous code. Previous options for asynchronous code are callbacks and promises. An `async function` returns an `AsyncFunction` object. Async/await, like promises, is non blocking and makes asynchronous code look and behave a little more like synchronous code.

```js
Object.getPrototypeOf(async function (){})
// AsyncFunction {}
```

async/await is built on top of promises. It cannot be used with plain callbacks.
`async` is the keyword that specifies that a function is `asynchronous`. `await` is the operator used to wait for a promise to be fulfilled. It can only be used inside an `async function`.

```js
async function () {
    // await
}
```
Let's build an example following the same idea of getting a random number.

```js
const getRandomWithAsync = async () => {
    try {
        return await getRandomWithPromise();
    } catch(error) {
        return error;
    }
}
```

The first thing to notice is the keyword `async` which reveals our arrow function as an asynchronous function. Our `await` operator takes the promise `getRandomWithPromise` and pauses the function execution until the promise has been fulfilled.

When fulfilled the promise can be:
* **resolved** - meaning that `await` will return the resolved value.
* **rejected** - meaning that `await` will thrown the rejected value.

Because a promise can thrown an error it is important to wrap our code with a `try / catch` block. Please note that the body of the function reads as an almost synchronous function. There's no longer the need for indentation as with promise chain.

Here's an example of use.

```js
// An async function returns a promise, therefore it can be used with promise chain.
getRandomWithAsync()
    .then((result) => {
        console.log(`Your random number is ${result}!`);
    })
    .catch((error) => {
        console.log(`oops! Something went wrong! Details: ${error}`);
    });
```

`await` can only be used inside an `async function`. If we write it outside a function,  it will result in syntax error.
```js
var random = await getRandomWithAsync(); // Syntax Error.
```

In order to use `await` as described in the line above we need to wrap it around an `async IIFE`.
```js
(async function () {
    console.log(`Your random number is ${await getRandomWithAsync()}!`);
})();
```

#### Classes

We can also create `async functions` inside `classes`.
```js
class Random {
    async getRandom () {
        return await getRandomWithPromise();
    }
}

(async function () {
    var random = new Random();
    console.log(`Your random number is ${await random.getRandom()}!`)
})();
```

#### Multiple Promises

Sometimes we need to wait for multiple promises to fulfill. We can do it in two ways - `sequentially`  and `concurrently`.

##### Sequentially
```js
(async function () {
    // Wait for the first promise to be fulfilled.
    var a = await getRandomWithPromise();
    // Wait for the second promise to be fulfilled.
    var b = await getRandomWithPromise();

    console.log(`Your random numbers are ${a} and ${b}!`);
})();

```
This handling is complete sequential. Our promise `b` is only executed after our promise `a` fulfills. But this is a major performance issue because we could have run both promises concurrently and that would take less time.

##### Concurrently
```js
(async function () {
    // Request the random numbers and save the promises.
    var aPromise = getRandomWithPromise();
    var bPromise = getRandomWithPromise();

    // At this point, the requests were both executed concurrently, meaning
    // that we didn't wait for the first to finish to request for the second.
    // We now just need to wait for both promises to be fulfill.
    var a = await aPromise;
    var b = await bPromise;

    // The function execution time would be equal to the promise that takes the most time.
    console.log(`Your random numbers are ${a} and ${b}!`);
})();
// [Execution Time] 0.283 ms total
```

##### Concurrently (with `Promise.all`)
One of the advantages with `Promise.all` is that it has the fail-fast behavior. If a promise fails, `Promise.all` will not wait for the other promises to be fulfill. It rejects immediately.

```js
(async function () {
    var [a,b] = await Promise.all([
        getRandomWithPromise(),
        getRandomWithPromise()
    ]);

    console.log(`Your random numbers are ${a} and ${b}!`);
})();
```

#### await and thenable
The use of the `await` operator is not restricted to promises.

```js
(async function () {
    var random = await 3;
    // await will convert any non-promise value into a promise value, meaning that
    // await will wrap the followed expression into 'Promise.resolve'.
    console.log(random); // 3
})();
```

`await` can be used with any object with a `.then()` method (i.e. a thenable).

```js
var thenable = {
    then(resolve) {
        resolve(45);
    }
};

(async function () {
    // Because await wraps the followed expression into 'Promise.resolve'
    // it will work with any thenable object (i.e. an object with a .then() method).
    var random = await thenable;

    console.log(random); // 45
})();
```

## Shared memory and atomics

From the specification:
> Shared memory is being exposed in the form of a new SharedArrayBuffer type; The new global Atomics object provides atomic operations on shared memory locations, including operations that can be used to create blocking synchronization primitives.

Shared memory means multiple threads can read and write the same data in memory. Here we can use the Atomics object to make sure nothing that is being written or read will be interrupted in the middle of the process. Atomic operations make sure that predictable values are written and read, that operations are finished before the next operation starts and that operations are not interrupted. This introduces a new constructor `SharedArrayBuffer` and a namespace object [Atomics](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Atomics) with static methods.

### Further Reading

* [ECMAScript® 2017 Language Specification](https://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [ECMAScript proposals](https://github.com/tc39/proposals)
* [Async Function Definitions](https://tc39.github.io/ecma262/2017/#sec-async-function-definitions)
* [How one programmer broke the internet by deleting a tiny piece of code](https://qz.com/646467/how-one-programmer-broke-the-internet-by-deleting-a-tiny-piece-of-code)

------
