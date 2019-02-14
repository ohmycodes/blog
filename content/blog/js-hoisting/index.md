---
title: JS Hoisting
date: '2019-02-11T23:00:00.000Z'
tags: ['javascript', 'functional', 'programming', 'fundamentals']
---

If you looked at [JS Scopes](https://www.ohmycode.cl/js-scopes/) post
you know about the two steps during compilation time. In the first step
we have our variables and functions declarations added to memory before
any execution.

So the concept of **_hoisting_** in this first step allows you to
declare variables and functions at the end of your code and use them
before their declarations but this can not be done over function
expressions.

```javascript
greeting = 'Hello world'
console.log(greeting)
var greeting
```

Above code will output `Hello world` even when the variable declaration
was at the bottom of the code. This is because our code is _hoisted_ at
_compilation time_ and looks something like this

```js
var greeting // declaration
greeting = 'Hello world' // assign
console.log(greeting)
```

You could say that our declarations are moved to the beginning of the
code (_hoisting_), but in reality our code stays where it was written.

```javascript
greeting = 'Hello world'
console.log(farewell) // undefined
console.log(greeting) // Hello world
helloJohn() // hello john
function helloJohn() {
  console.log('hello john')
}
var greeting
var farewell = 'bye bye'
var helloWorld = seyHello() // RefrenceError...
var sayHello = function() {
  return greeting
}
```

In the above code we receive an `undefined` response when try to
console log variable _farewell_ and a `ReferenceError: seyHello is not defined` when try to call the _sayHello_ function and this is because
**_initializations_** and **_function expressions_** are not _hoisted_.
Above code can be read like below when hoisted

```js
function helloJohn() {
  console.log('hello john')
}
var greeting
var farewell
var helloWorld
farewell = 'bye bye'
helloWorld = sayHello()
sayHello = function() {
  return helloJohn()
}
```

### What about ES6 _let_ and _const_?

- Declarations using **_const_** are not _hoisted_ you need to
  initialize it immediately and you cannot re assign them
- Declarations using **_let_** do _hoisting_ but what it doesn't do is
  initialize at the beginning of the environment scope (unlike **_var_**)

```js
function sayHello(greeting) {
  if (greeting) {
    console.log(hi) // ReferenceError: hi is not defined
    let hi = greeting
  }
}
sayHello('Hello world')
```
