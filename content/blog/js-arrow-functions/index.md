---
title: JS Arrow Functions
date: '2019-02-26T23:00:00.000Z'
tags: ['javascript', 'functional', 'programming', 'fundamentals']
---

* _Arrow function_ were introduced to Javascript in _EMAScript 6_
* Sintactically compact alternative to a regular _function expression_
* Also called fat arrow functions because it shape **_=>_** they are always anonymous

**_Regular function expression_**

```js
function sayHello() {
  console.log("Hello World");
}
sayHello(); // "Hello World"
```

**_Arrow function expression_** (needs to be assign because it is a function expression)

```js
var sayHelloWorld = () => console.log("Hello World"); 
sayHelloWorld(); // "Hello World"
```

## Syntax examples

- If doesn't have parameters then you can use just a pair of _parentheses_

```js
// no body if you have just one expression
var sayHelloWorld = () => console.log("Hello World"); 
sayHelloWorld(); // "Hello World"
// body surronded by {} when you have more expressions
var sayHelloWorld = () => {
  console.log("Hello World");
  console.log("Hello World");
  console.log("Hello World");
}
```

- If paramaters are present then you must surround them using _parentheses_ except if you have just one paremeter in that case you can avoid them

```js
// with just one paremeter as placeholder
var a = x => 1 + x;
a(2); // 3

// with more than one parameter use parentheses
// implicit return -> return a + b
var sum = (a, b) => a + b;
sum(1, 2); // 3

// with paramters and statements
var addAndMultiplyBy10 = (a, b) => {
  let sum = a + b;
  return sum * 10;
}
addAndMultiplyBy10(1, 2); // 30
```

- To return an object literal expression parenthesize _()_ the body

```js
var person = (name, age) => ({ name: name, age: age});
var userJohn = person("John", 18);
console.log(userJohn.name, userJohn.age); // "John 18"
```

- Rest parameters and default parameters are supported

```js
var person = (name, age = 18, ...courses) => ({ name: name, age: age, courses: courses});
var userJohn = person("John", undefined, "JS", "Java", "Algebra");
console.log(userJohn); // "{name: "John", age: 18, courses: ["JS", "Java", "Algebra"]}"
```

- Destructuring the parameter list is supported

```js
var userJohn = {name: "John", age: 18, courses: ["JS", "Java", "Algebra"]};
var person = ({name = "", lastname = "Doe", age = 99, courses = [""]}) => 
	({ name: name, lastname: lastname, age: age, courses: courses});
console.log(person(userJohn));
// "{name: "John", lastname: "Doe", age: 18, courses: ["JS", "Java", "Algebra"]}"

var fruits = ["orange", "apple", "banana"];
var fruitsLength = fruits.map( ({length}) => length );
console.log(fruitsLength); // [6, 5, 6] 
```

- You can use concise body without _{}_ if you surround your body with parentheses and separate statements by comma (*_,_*) This is a common practice used in arrow functions to avoid the use of _{}_ and the _;_ that separate th statements expressions

```js
var addAndMultiplyBy10 = (a, b) => (sum = a + b, console.log(sum * 10));
addAndMultiplyBy10(5, 5); // 100
```

- They don't have it own binding of `this` at it scope so it adopts the one in the outer function.

```js
// ES5
function requestGreeting(gender, male, female) {
  if (gender == "male") male();
  else female();
}
let user = {
  male: 'John',
  female: 'Jane',
  maleGreeting() {
    console.log(`Hello Mr ${this.male}`);
  },
  femaleGreeting() {
    console.log(`Hello Mrs ${this.female}`);
  },
};
requestGreeting('female', user.maleGreeting.bind(user), user.femaleGreeting.bind(user));
// Hello Mrs Jane
```

```js
// ES6
function requestGreeting(gender, male, female) {
  if (gender == "male") male();
  else female();
}
let user = {
  male: 'John',
  female: 'Jane',
  maleGreeting() {
    console.log(`Hello Mr ${this.male}`);
  },
  femaleGreeting() {
    console.log(`Hello Mrs ${this.female}`);
  },
};

requestGreeting('male', () => user.maleGreeting(), () => user.femaleGreeting()); 
// Hello Mr John
```
