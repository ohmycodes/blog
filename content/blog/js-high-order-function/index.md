---
title: High Order Function
date: '2019-01-29T23:00:00.000Z'
tags: ['javascript', 'functional', 'programming', 'fundamentals']
---

A HOF is a function that can take another function as an argument (i.e. a _callback_) or that can return a function.
A `Callback` is a function that can be passed as an argument.

- Example of **HOF** that takes a function as an argument

```javascript
// declare parameter callbackFn
function takingFunctionAsAnArgument(callbackFn) {
  callbackFn()
}

// to use the HOF
// declare the function passed in the argument
function consoleLogMessage() {
  console.log('Hello World')
}
// call the HOF passing the function declared above
// remember that a function is called when parentheses are used after the name of the function
takingFunctionAsAnArgument(consoleLogMessage)
// output: Hello World
```

- Example of **HOF** that take return another function

```javascript
// declare function named functionThatAddTwoNumbers that returns function add
function functionThatAddTwoNumbers(a, b) {
  return function add() {
    return a + b
  }
}
// assign the value returned by the execution of functionThatAddTwoNumbers function that is another function so result now is a function
let result = functionThatAddTwoNumbers(1, 4)
// result is a function so we need parentheses to call it
result()
```

In order to understand better this concept let's see a simple demonstration code and later we will break it down.

```javascript
function cloneOfMap(array, fn) {
  let output = []
  for (let i = 0; i < array.length; i++) {
    output.push(fn(array[i]))
  }
  return output
}

function add10(input) {
  return input + 10
}

let result = cloneOfMap([1, 2, 3], add10)
console.log(result)
```

**_Breaking down the code:_**

1. In _global memory_ declares a function named `cloneOfMap`
2. In _global memory_ declares a second function named `add10`
3. In _global memory_ declares a variable labeled as `result` with value _undefined_ at the moment
4. `result` receive the value of `cloneOfMap([1, 2, 3], add10)` method execution. We know this because a function is executed when we use _*()* - parentheses_ next to its name
5. `cloneOfMap(..)` method is pushed to _javascript callstack_
6. A new `execution context` is created in _local memory_ now that we are inside of `cloneOfMap([1, 2, 3], add10)` function

   - In the _local memory_, the parameter _array_ take the value of argument _[1, 2, 3]_
   - In the _local memory_, the parameter _fn_ take the value of argument _add10_
   - In the _local memory_, the variable _output_ is declared as an empty array
   - In the execution thread, we use a `for` statement to iterate over the array _[1, 2, 3]_ First we take the first index in the array _i=0 => array[0]=1_
   - We push to the _output_ array the value returned in the execution of `fn(array[i])`.
   - The `fn(1)` method is pushed to the _javascript callstack_
   - A new `execution context` is created (_fn(1) => add(1)_) - In the _local memory_, the parameter _input_ take the value of argument **_1_** (first element in array [***1***, 2, 3]) - In the execution thread we evaluate _1 + 10_ - We return number _11_ (from the evaluated expression) - After return `fn(1)` is popped from the _javascript callstack_ and we return to the execution of `cloneOfMap(..)` function
   - **_output_** now is **_*[11]*_**
   - Now we look for the second index in the array _i=1 => array[1]=2_
   - Again, we push to the _output_ array the value returned in the execution of `fn(array[i])`.
   - The `fn(2)` method is pushed to the _javascript callstack_
   - A new `execution context` is created (_fn(2) => add(2)_) - In the _local memory_, the parameter _input_ take the value of argument **_2_** (second element in array [1, ***2***, 3]) - In the execution thread we evaluate _2 + 10_ - We return number _12_ (from the evaluated expression) - After return `fn(2)` is popped from the _javascript callstack_ and we return to the execution of `cloneOfMap(..)` function
   - **_output_** now is **_*[11, 12]*_**
   - Now we look for the third index in the array _i=2 => array[1]=3_
   - Again, we push to the _output_ array the value returned in the execution of `fn(array[i])`.
   - The `fn(3)` method is pushed to the _javascript callstack_
   - A new `execution context` is created (_fn(3) => add(3)_) - In the _local memory_, the parameter _input_ take the value of argument **_3_** (second element in array [1, 2, ***3***]) - In the execution thread we evaluate _3 + 10_ - We return number _13_ (from the evaluated expression) - After return `fn(3)` is popped from the _javascript callstack_ and we return to the execution of `cloneOfMap(..)` function
   - **_output_** now is **_*[11, 12, 13]*_**
   - `cloneOfMap(..)` then returns value **_*[11, 12, 13]*_** and is popped from the _javascript callstack_ and we return to the _global_

7. **_*[11, 12, 13]*_** is the value of **_result_** variable in the _global memory_ (remember that before was _undefined_)

Next slide can help to clarify what it is described above (that helped me to understand -in a certain way- how javascript works), thanks to [Will Sentance](https://twitter.com/willsentance) from [Codesmith](https://www.codesmith.io/) I saw him teaching the hard parts of javascript.

<iframe src="//slides.com/mauriciogaldames-1/p/embed" width="576" height="420" scrolling="no" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>

Also, you can try the code in [latenflip.com](http://latentflip.com/loupe/?code=ZnVuY3Rpb24gY2xvbmVPZk1hcChhcnJheSwgZm4pIHsKICBsZXQgb3V0cHV0ID0gW10KICBmb3IgKGxldCBpID0gMDsgaSA8IGFycmF5Lmxlbmd0aDsgaSsrKSB7CiAgICBvdXRwdXQucHVzaChmbihhcnJheVtpXSkpCiAgfQogIHJldHVybiBvdXRwdXQKfQoKZnVuY3Rpb24gYWRkMTAoaW5wdXQpIHsKICByZXR1cm4gaW5wdXQgKyAxMAp9CgpsZXQgcmVzdWx0ID0gY2xvbmVPZk1hcChbMSwgMiwgM10sIGFkZDEwKQpjb25zb2xlLmxvZyhyZXN1bHQp!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D) to know how callstack works.
