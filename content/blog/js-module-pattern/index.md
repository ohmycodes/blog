---
title: JS Module Pattern
date: '2019-02-14T23:00:00.000Z'
tags: ['javascript', 'functional', 'programming', 'fundamentals']
---

The idea of ​​this pattern is _encapsulation_, which hides the implementation to prevent someone from manipulating it. This pattern leverage his power in [closures](https://www.ohmycode.cl/js-closures/) and can be created as a function.

There are two characteristics that needs to be present in order to be a considered as _module pattern_

1. There must be an outer enclosing function invoked at least on time (_CounterModule()_ in our example)
2. There must be at least one inner function returned by the outer enclosing function and must have closure over the private scope (_getCounter()_ and _incrementCounter()_ in our example)

## ES5 modules

The function module can be invoked any time and will create a new instance of the module on each invocation (_factory_)

```js
function CounterModule() {
  var counter = 0
  function getCounter() {
    return counter
  }
  function incrementCounter() {
    counter++
  }

  return {
    getCounter: getCounter,
    incrementCounter: incrementCounter,
  }
}

var myCounter = CounterModule()
var myCounter2 = CounterModule()
console.log(myCounter.getCounter()) // 0
console.log(myCounter.incrementCounter())
console.log(myCounter.getCounter()) // 1
console.log(myCounter.incrementCounter())
console.log(myCounter.getCounter()) // 2
console.log(myCounter2.getCounter()) // 0
```

Using _IIFE design pattern_ we can create a single module instance (_singleton_) because the function is immediately invoked and assign its return value to the target

```js
// will be executed 1 time thanks to the IIFE
var myCounter = (function CounterModule() {
  var counter = 0
  function getCounter() {
    return counter
  }
  function incrementCounter() {
    counter++
  }

  return {
    getCounter: getCounter,
    incrementCounter: incrementCounter,
  }
})()

console.log(myCounter.getCounter()) // 0
console.log(myCounter.incrementCounter())
console.log(myCounter.getCounter()) // 1
console.log(myCounter.incrementCounter())
console.log(myCounter.getCounter()) // 2
```

The object returned by the module function has references to our inner functions not to our inner data variables these are keeped hidden and private (encapsulation).

If you keep an inner reference to the object inside your module you are going to be able to modify the module instance from inside

```js
var myCounter = (function CounterModule() {
  var counter = 0
  function getCounter() {
    return counter
  }
  function incrementCounter() {
    counter++
  }
  function incrementCounterBy2() {
    counter += 2
  }
  function changeCounter() {
    myAPI.incrementCounter = incrementCounterBy2
  }

  var myAPI = {
    getCounter: getCounter,
    incrementCounter: incrementCounter,
    changeCounter: changeCounter,
  }

  return myAPI
})()

console.log(myCounter.getCounter()) // 0
console.log(myCounter.incrementCounter())
console.log(myCounter.getCounter()) // 1
console.log(myCounter.changeCounter())
console.log(myCounter.incrementCounter())
console.log(myCounter.getCounter()) // 3
```

## ES6 modules

- A file with js code is by default a module and it is a singleton
- Using the keyword `export` before it declaration you can export multiple things
- You can have multiple exports using _named exports_ (as in the example below) or just one export per module using the keyword `default`
- To use functionality from a particular module you can use the keyword `import` and declare what you need from the module
- _imports_ are hoisted

**_calculator.js_**

```js
export function add(a, b) {
  return a + b
}
export function multiply(a, b) {
  return a * b
}
export function square(a) {
  return a * a
}
```

**_defaultModule.js_**

```js
export default function(a) {
  console.log(a)
}
```

**_index.js_**

```js
import { add, multiply, square } from 'calculator'
import defaultModule from 'defaultModule'

console.log(add(1, 2)) // 3
console.log(multiply(2, 2)) // 4
console.log(square(2)) // 4
defaultModule('hello world') // "hello world"
```
