---
title: Closures
date: '2019-01-30T23:00:00.000Z'
tags: ['javascript', 'functional', 'programming']
---

First time I knew about the concept of `Closure` was using _Groovy_ and later when I started learning _Javascript_ I met it again and they look like the same but not at all.
In _Javascript_ a _Closure_ is a special kind of [High Order Function](https://www.ohmycode.cl/js-high-order-function/) where a function returns another function that can be assigned to a label and then called. Then the function can _remember_ the variables around it even when that function is called from another place.

Remember that functions in javascript are first class objects and they can be:

- Assigned to variables and properties of other objects
- Passed as arguments in functions
- Returned as values from a function (_closure_)

```javascript
function greetingFunction() {
  function printHello() {
    console.log('hello there!')
  }
  return printHello
}

// assign the value returned by the execution of greetingFunction so sayHello is like an alias of printHello :/
var sayHello = greetingFunction()
sayHello()
// output: hello there!
```

An advantage of using _closures_ in a function is that they allow you to do _memoization_ (thanks to _[[scope]]_ - _lexical scope_) using it to maintain the state that can't be observed beyond that function.

> Lexical scope is the process of putting one function in scope and perhaps within another function referencing variables and accessing them out of scope.

When a function is called a kind of _data store_ is created for the _execution context_ of that function. After the function execution its local memory is deleted (garbage collector is called automatically) but the returned value it is not deleted.

> The _execution context_ is defined as the environment in which Javascript code is executed (variable scope, function arguments, and the value of the _this_ object)

The next code uses a closure to memoize a multiplication by 10. Every time a number is passed to the function will be checked if exists in the _cache_ object. If not exists then it will be calculated and saved in the object (_number_: _number_times_10_) but if the number exists in the object then it will get the value from the cache object and return it immediately.

```javascript
const times10(n) {
  return n * 10;
}

const function memoizedClosureTimes10() {
   const cache = {};
   return function(n) {
    if (n in cache) {
      console.log('cached!');
      return cache[n];
    } else {
      console.log('not cached')
      let newVal = times10(n);
      cache[n] = newVal;
      return newVal;
    }
  }
};

const memoClosureTimes10 = memoizedClosureTimes10();
memoClosureTimes10(9);  // not cached so execute times10(n), cache and return
memoClosureTimes10(9);  // cached! returns from cache
memoClosureTimes10(9);  // cached! returns from cache
```

Now let's see another closure example

```javascript
function outerCounterFunction() {
  let counter = 0
  function innerIncrementCounterFunction() {
    counter++
  }
  return innerIncrementCounterFunction
}

let incrementCounter = outerCounterFunction()
incrementCounter()
incrementCounter()
```

In the next slide we can break down the execution of the above code

<iframe src="//slides.com/mauriciogaldames-1/closures/embed" width="576" height="420" scrolling="no" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>
