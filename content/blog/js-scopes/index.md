---
title: JS Scopes
date: '2019-02-04T23:00:00.000Z'
tags: ['javascript', 'functional', 'programming']
---

**Scope** means where should I search for _things_ and it is a set of rules that allows order things and thanks to this find them in this way search them efficiently.

When compiling in Javascript, two steps are carried out (_JIT - Just In Time - compiler_)

1. A program validation (_parsing_) where the _lexical scope_ is made
2. Executes step _1._

```javascript
var greeting = 'hello'
function salute() {
  var greeting = 'hi'
}
function saludo(greeting) {
  greeting = 'hola'
  salutation = 'hey'
}
```

1. Javascript at compilation time is analyzing the codebase declarations and is ordering the scope
   - _greeting_ variable is in the global scope
   - _salute_ function declaration is in the glogal scope
     - _greeting_ variable declaration inside _salute_ function is _shadowing_ greeting variable declared in the begining and belongs to the _salute_ function scope
   - _saludo_ function declaration is in the global scope
     - parameter _greeting_ is searched inside _saludo_ scope and is saved in _saludo_ scope
     - _salutation_ couldn't be considered correct because it doesn't have _var_ or _let_

> In the assignment operation the left side of the _=_ is the _target_ and the right side is the _source_ > `var target = source`

2. Code execution can be interpreted as a conversation between the _Javascript Virtual Machine_ and the _Global Scope Manager_ (here I need to thank [Kyle Simpson](https://twitter.com/getify) because he explains this in a way that you can understand, you should read his books _You Don't Know JS_)

  - `var greeting = "hello"`
    - JSVM: "hey global scope manager I have a target reference to a variable named _greeting_ do you have one?"
    - GSM: "yes, I do"
    - JSVM: "thanks" so now is time to assign _"hello"_ to variable _greeting_
  - `var greeting = "hi"` inside _salute() {..}_
    - JSVM: "hey scope of _salute_ I have a (target) reference to _greeting_ Do you heard of it?"
    - SOS: "yes, I do"
    - JSVM: "thank you scope of _salute_" so now it is time to assign _"hi"_ to _greeting_
  - `salutation = "hey"` inside _saludo(greeting) {..}_
    - JSVM: "hey scope of _saludo_ I have a (target) reference for _salutation_ Do you know him?"
    - SOS: "No, I don't"
    - JSVM: "Ok no problem... hey global scope manager I have a (target) reference for _salutation_ Do you know him?"
    - GSM: "No, I don't. But don't worry I can create one in execution time"
    - JSVM: "Oh thank you I owe you a beer" so now it is time to assign _"hey"_ to variable _salutation_ in global (_implicit creation_)

> Even though javascript allows implicit creation in global scope as programmers we should avoid this bad practice. So we can add _"use strict"_ mode to our code.

### Lexical Scope

Inner functions contain the scope of parent functions even if the parent function has returned

```javascript
function sayHello() {
  var greeting = 'Hi there'
  function hi() {
    console.log(greeting) // lexical
  }
  hi()
}
sayHello()
```

### Dynamic Scope

In a language with dynamic scope it doesn't matter where the function was written or in what form it was called. It only matters where it was called from.

```javascript
function hi() {
  console.log(greeting) // dynamic
}
function sayHello() {
  var greeting = 'Hi there'
  hi()
}
sayHello()
```

### Function Scoping

Is the best way to create a _scope_ in case you want to protect something (like a variable) either from names collision or to protect access.

Imagine that we have a codebase with a lot of lines, where we need to use the scroll to navigate through it and we can't remember if we already declare a variable with the same label, this is what could happen:

```javascript
var greeting = 'hello world'
// ..
// .... alot of code between
// ..
var greeting = 'hola mundo'
console.log(greeting) // hola mundo
// ... more code between
console.log(greeting) // hola mundo
```

Our solution using function scope to avoid names collision could be

```javascript
var greeting = 'hello world'
function sayHelloInSpanish() {
  var greeting = 'hola mundo'
  console.log(greeting) // hola mundo
}
sayHelloInSpanish()
console.log(greeting) // hello world
```

This solution can bring the problem of pollute the global scope with a names collision because we need to declare a named function. **_IIFE_** pattern can help us to solve this problem.

> - Anonymous functions doesn't have a name _function() {..}_
> - Named functions have a name _function sayHello() {..}_ and have some benefits like 1. self-reference friendly 2. can be debugged in stacktraces 3. self documented code

#### IIFE pattern

**IIFE** means _Immediately Invoked Function Expression_ and is a Javascript function that runs as soon as it is defined.

```javascript
;(function() {
  statements
})()
```

To avoid names collision in the global scope we can refactor our last code using this pattern

```javascript
var greeting = 'hello world'
;(function sayHelloInSpanish() {
  var greeting = 'hola mundo'
  console.log(greeting) // hola mundo
})()
console.log(greeting) // hello world
```

Breaking down the code

```javascript
;(function sayHelloInSpanish() {
  var greeting = 'hola mundo'
  console.log(greeting) // hola mundo
})()
```

- The surrounding parentheses transform the function in a _function expression_ instead of a _function declaration_
- The parentheses at the end make the function run immediately

### Block Scoping

A block scope is what you get when you use some statements like _if_, _for_, other _loops_ and you can create them with curly braces _{}_ but this can't be confused with empty object literals

Before and during _ES6_ you can declare a block scope with _var_

```javascript
function diff(x, y) {
  if (x > y) {
    var tmp = x
    x = y
    y = tmp
  }
  return y - x
}
```

- _tmp_ is hoisted and can be used inside _diff_ function scope and not exclusively inside the _if_ statement this can lead to some bugs in enterprise applications

If you want that _tmp_ lives just in the _if_ statement in _ES6_ you can use _let_

```javascript
function diff(x, y) {
  if (x > y) {
    let tmp = x
    x = y
    y = tmp
  }
  return y - x
}
```

- _let_ strictly create a block scope inside _if_ statement and _tmp_ variable can be used just in here
- if _tmp_ variable is used outside the _if_ statement then a _ReferenceError..._ is thrown
- the curly braces _{}_ in the _if_ statement are the indicators of the block scope
- the name _let_ is a aesthetic signal that reinforces what aesthetically is doing

#### Block scoping with _var_ and _let_

```javascript
function add10(arr) {
  var result = 0
  for (let i = 0; i < arr.length; i++) {
    result = i + 10
  }
  // console.log(i) // ReferenceError...
  return result
}
```

- `var result` indicates that scope of _result_ is the whole function
- _let_ in the _for loop_ indicates that we want the variable _i_ just live inside the loop statement and not in the rest of the function

Sometimes is better to use _var_ instead of _let_ to keep the scope, for example in a _try/catch_

#### Block scoping with _const_

A variable declared with _const_ indicates that cannot be reassigned. And that variable will not change in that block scope.
Always use _const_ with an immutable value

```javascript
const LUCKY_NUMBER = 5
LUCKY_NUMBER = 7 // Uncaught TypeError: assignment to constant variable at...

const MY_ARRAY = []
MY_ARRAY.push(1) // [1]
MY_ARRAY.push(2) // [1, 2]
MY_ARRAY[0]++ // [2, 2]

const MY_OBJECT = { key: 'value' }
MY_OBJECT.key = 'otherValue' // {"key": "otherValue"}
```
