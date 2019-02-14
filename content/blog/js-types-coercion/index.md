---
title: JS Types & Coercion
date: '2019-02-13T23:00:00.000Z'
tags: ['javascript', 'functional', 'programming', 'fundamentals']
---

## Types

Type (or data type) is a characteristic of a value affecting what kind of data it can store — for example in JavaScript a Boolean only holds true/false values, whereas a String holds text strings, a Number holds numbers of any kind, and so on.

A value's data type also affects which operations are valid on that value. For example, an integer can be multiplied by an integer, but not by a string. [from MDN Web Docs](https://developer.mozilla.org/en-US/docs/Glossary/Type)

**_Primitives_** are the built-in immutables types:

- null
- undefined
- boolean
- number
- string
- symbol (since ES6)
- object

> If you want to know the actual value in a variable you can use _typeof_ => `typeof 36 // "number"`

**_Natives_** types are primitive wrapper objects and they were created to simplify developers life (specially java developers):

- String
- Number
- Boolean
- Array
- Object
- Date
- _..._

Some of the natives types are built-in objects and others created by developers.

```js
var a = 'hello' // primitive
a.toUpperCase() // "hello"

var b = new String('hello') // object
b.toUpperCase() // "HELLO"

var arr = new Array(2) // with 2 empty slots
arr.push(1) // [undefined, undefined, 1]
arr[0] // undefined
arr[2] // 1
var arrLit = [1, 2, 3, 4, 5] // create array using literal
```

## Coercion

This is a Javascript feature where a value is converted from one type to another (type conversion) implicitly and always results in one of the scalar primitives (string, number or boolean).

### Abstract Operations

**_ToString_** will handle coercion of any _non-string_ value to a _string_ representation

```js
// applying to primitives values
String(null) // "null"
String(undefined) // "undefined"
String(true) // "true"
String(false) // "false"
String(0) // "0"
String(-0) // "0"
// applying to arrays
String([]) // ""
String([1, 2]) // "1,2"
;[1, 2]
  .toString() // "1,2"
  [(null, undefined)].toString() // ","
;[[], [], []].toString() // ",,"
// applying to objects has no representation
var obj = { a: 1, b: 2 }
obj.toString() // [object Object]
```

**_ToNumber_** is the abstract operation that handle coercion of _non-number_ values to a _number_ representation

```js
Number('') // 0
Number('-0') // -0
Number('0') // 0
Number('007') // 7
Number(007) // 7
Number('0,9') // NaN
Number('0.9') // 0.9
Number('0xaa') // 170
Number('.') // NaN
Number('mau') // NaN
Number([1, 2, 3]) // ToString -> ToNumber -> NaN
```

Objects and arrays will first be converted to their primitive value, so the _ToPrimitive_ abstract operation will look for _valueOf()_ method to return a primitive value that will be used for coercion. And if _toString()_ is available then it will provide the value for the coercion.

```js
var a = {
  valueOf: function() {
    return 36
  },
}
Number(a) // 36
```

**_ToBoolean_** will handle values coercion to boolean, JS spec defines a specific list of values that coerce to _false_ (_*falsy values*_)

- _undefined_
- _null_
- _false_
- _0_, _+0_, and _-0_
- _NaN_
- _""_

Any value not present in the above list will be called a _truthy_ value

```js
// falsy
Boolean(0) // false
Boolean(-0) // false
Boolean(+0) // false
Boolean(undefined) // false
Boolean(null) // false
Boolean(NaN) // false
Boolean('') // false

// truthy
Boolean("''") // true
Boolean('false') // true
Boolean(1) // true
Boolean(2) // true
Boolean([]) // true
Boolean({}) // true
```

### Explicit Coercion

Is when you watch the code and you know what type conversion is being done, they are obvious and make the code more understandable.

```js
// coerce number to string using ToString rules
var s = 36
String(s) // "36"
// coerce number to string using .toString()
s.toString() // "36" <- js automatically boxes number in an object wrapper
// coerce string to number using ToNumber rules
Number('36') // 36
// coerce string to number using _+_ unary operator
var a = +'36' // 36
// do not use + operator immediately adjacent to other operators

// Date to number using + operator
var d = new Date('2019-02-11 10:00:00')
;+d // 1549890000000
var ts = +new Date() // 1549890000000

var ts = Date.now() // USE THIS!!!

// coerce to Boolean using _!_ unary operator
var t = '1'
!!t // true
var f = ''
!!f // false
```

Explicit boolean coercion with **_!_** unary operator: _!!"1"_ the second _!_ operator (from left to right) apply ToBoolean to what is on it right side if it is not boolean, and the first _!_ negates the boolean obtained

```
 !!"1"
  - "1" is true
  - !true is false
  - !false is true
 result is true
```

The **_~_** unary operator can also be used to do an explicit boolean coercion when used with _indexOf(..)_ method

- `~n = -(n + 1) => ~26 = -(26 + 1) = -27`
- Failure value _-1_ is returned by _indexOf(..)_ when no substring is found in another string
  - `~"some string".indexOf("substring")`
    - `"some string".indexOf("substring") returns -1`
    - `~"some string".indexOf("substring") => -(-1 + 1) => -0 => 0 (bitwise not arithmetic) => falsy`

### Implicit Coercion

Are any type conversions that are not obvious, making the code more difficult to understand because you don't know what goes on behind.

The intent of the _implicit_ coercion is:

- Reduce verbosity
- Reduce boilerplate
- Reduce unnecessary implementation detail
- Aids in the readability of the code

Implicit coercion from _string to number_

```js
// -, * and / operators are defined just to numeric operations
var a = '23'
var b = a - 0 // 23
var c = a / 1 // 23
var d = a * 2 // 46

// first coerced to string and then coerced to number
var e = [24] - [1] // 23
var f = [24, 1] - [1, 1] // NaN <- Number("24,1") - Number("1,1")
```

Implicit coercion from _number to string_

```js
// using + operator
var a = 23
var b = a + '' // "23"

// ToNumber rules on objects
// The valueOf() operation on the array will fail to produce a simple primitive,
// so it then falls to a toString() representation "1,2" and "3,4"
// and finally + concatenates both string
var a = [1, 2] + [3, 4] // "1,23,4"

var a = {
  valueOf: function() {
    return 36
  },
  toString: function() {
    return 23
  },
}
a + '' // "36" <-- invokes valueOf(..)
String(a) // "23" <-- invokes toString()
```

Implicit coercion from _boolean to number_

```js
//
var a = 1 + true // 2 <-- true coerced to 1
var a = 1 + false // 1 <-- false coerced to 0
```

Implicit coercion from _anything to boolean_ are required in test expressions:

- _if()_ statement
- _for( ; .. ; )_ second clause
- _while()_ and _do .. while()_ loops
- first clause in ternary expresion _[condition] ? [when-true] : [when-false]_
- left-hand operand of _||_ (logical or) and _&&_ (logical and) operators

```js
var a = 36
var b = 'hello'
var c // undefined
var d = null

if (a) {
  // 36 -> truthy
  console.log('printed') // printed
}

while (c) {
  // undefined -> falsy
  console.log('never run')
}

c = d ? a : b // false ? 36 : "hello" -> "hello"
c // "hello"

// (( 36 && null) || "hello")
// ((true && false) || true)
// false || true
// true
if ((a && d) || c) {
  console.log('printed') // printed
}
```

**_||_** and **_&&_** operators select the value of one of the two operands expressions, _*||*_ operator is commonly used to have a default value if the first evaluation is falsy

```js
var a = 1
var b = 'hello'
var c = undefined

a || b // 1
a && b // "hello"

c || b // "hello"
c && b // undefined

var name
var greeting = 'hello ' + (name || 'john') // "hello john" <- bc name is undefined then falsy
```

**_*==*_** operator allows coercion in the equality comparison so if you are comparing two values of different type you may want to use _==_ coercion, if not you can go with _===_ strict equals.

```js
// string to number
var a = 1
var b = '1'
a === b // false because === doesn't allow coercion
a == b // true because "1" is a string and is coerced with ToNumber rules

// anything to boolean
var c = '36'
var d = true
// "36" == true when an operand is boolean is coerced using ToNumber rules Number(true) => 1
// "36" == 1 now there is a string so is coerced with ToNumber rules too
// 36 == 1? No -> false
c == d // false
a == d // true "1" == 1 -> 1 == 1

// null and undefined
var e = null
var f
e == f // true
e == null // true
f == null // true
f == undefined // true
// using others false values
e == false // false
f == false // false
e == '' // false
f == '' // false
e == 0 // false
f == 0 // false

// objects to non-objects
// objects are coerced with the ToPrimitive algorithm
// remember valueOf() and toString()
var g = 36
var h = [36]
// 36 == [36]
// 36 == "36"
// 36 == 36
g == h // true

var i = 'hello'
var j = Object(i) // unboxed to "hello" using ToPrimitive
i == j // true
i === j // false
typeof i // "string"
typeof j // "object"
```

- _==_ is for numeric comparison so any type (strings, booleans) will be coerced to number
- Avoid _== true_ or _== false_ comparisons because you will be comparing to _1_ or _0_
- _NaN_ is never equal to _NaN_
- _-0_ and _+0_ are equal to each other
- _null_ and _undefined_ are equal to each other and themselves and will fail to other values
- _null_ and _undefined_ can't be boxed they don't have object wrapper equivalent
- You can just use `if(a == null){..}` instead of `if(a === undefined || a === null){..}`
- Example of a crazy coercion result `[] == ![] is true` How can a value be false and true at the same time? They can't
  - _![]_ the unary operator _!_ explicitly coerces to a boolean what is at the right side
    - _ToBoolean([])_ => true
    - Then _!_ operator negates the result _!true_ => false
  - Now we have _[] == false_ and we saw that booleans coerce to number
    - _false_ becomes _0_
  - Now we have _[] == 0_ and the object array coerce to a string _""_ using the ToPrimitive rules
    - _[]_ becomes _""_
  - Now we have _"" == 0_ and the string coerce to number
    - _""_ becomes _0_
  - Finally _0 == 0_ is true
