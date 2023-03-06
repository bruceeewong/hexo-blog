---
title: Data Structures in JavaScript
date: 2023-03-05 22:09:00
categories: [FrontEnd, JavaScript]
tags: [JavaScript, Data Structure]
---

In this article, I'm going to summarize everything you should know about data structures in JavaScript.

<!-- more -->

# Data Types

![image-20230305221757271](https://static.bruski.wang/picgo/20230305221759-f2ba827337ec4b57982bb100e3bdf16e.png)

The mind graph shows 7 primitive types (left) and 1 reference type `object`. Among the primitives, `Symbol` was introduced by ES2015 (ES6) and `BigInt` by ES2020 (ES11), which means before ES6 there are only 5 primitive types in JavaScript (`number`, `boolean`, `string`, `undefined`, `null`)

The difference between the primitive type and the reference type lies in their storage in memory:

- **Primitive types** are stored in the `Stack`. 
  - The value is stored in the stack.
  - Passed by value.
  - When referenced or copied, an equivalent variable would be created with the exact same value.
- **Reference types** are stored in the `Heap` .
  - The address that points to the actual value in the heap is stored in the stack (i.e. the reference to the value)
  - Passed by reference.
  - All references are pointing to the same heap value, which means modification to the value would affect all the references.

## Example 1 for References

```javascript
let a = {
  name: 'mike',
  age: 20
}
let b = a
console.log(a.name)  // mike
b.name = 'john'

console.log(a.name)  // john
console.log(b.name)  // john
```

Since `b` and `a` are all pointing to the same object value, modification from `b` affects the output of `a.name`

## Example 2 for References

```javascript
let a = {
  name: 'mike',
  age: 20
}
function change(o) {
  o.age = 24
  o = {
    name: 'john',
    age: 30
  }
  return o
}

let b = change(a)
console.log(b.age)  // 30
console.log(a.age) // 24
```

Inside the function `change`, we change the `age` of the input param to 24, that's the reason for the output of `a`. However, we assigned a new object (with its new address) to the input param `o`, so `o` then points to a new object. So `b` is assigned by the return value  `o`, a new object with age `30`.

# Data Type Detection

## Method 1: typeof

```javascript
typeof 1 // 'number'
typeof '1' // 'string'
typeof undefined // 'undefined'
typeof true // 'boolean'
typeof Symbol() // 'symbol'
typeof BigInt(0) // 'bigint'
typeof null // 'object'
typeof []  // 'object'
typeof {} // 'object'
typeof console // 'object'
typeof console.log // 'function'
```

We can see we should only use `typeof` for `number`, `string`, `undefined`, `symbol`, `bigint`, because it's not accurate for `null` and reference types (except for `function`). 

Notice that the result for `null` using `typeof` is `object`, the same for array `[]`. 

## Method 2: instanceof

```javascript
let Car = function () {}
let benz = new Car()
benz instanceof Car // true
let car = new String('BMW')
car instanceof String // true
let str = 'BMW'
str instanceof String // false
```

Notice that `literal string` is not the instance of `String` class.

Let's try to implement `instanceof` by ourself for further understanding!

```javascript
function isInstanceOf(value, type) {
  // if value is primitive type
  if (typeof value !== 'object' || value === null) return false
  // get the prototype object from value
  let proto = Object.getPrototypeOf(value);
  while (true) {
    if (proto === null) return false
    if (proto === type.prototype) return true  // found the same prototype
    proto = Object.getPrototypeOf(proto)  // all the way up through the prototype chain
  }
}

// testings
console.log(isInstanceOf(new Number(123), Number))  // true
console.log(isInstanceOf(123, Number))  // false
```

`instanceof` can precisely check reference types, but not for primitive types.

So is there a perfect way to check all the data types? Yes, there is.

## Method 3: Object.prototype.toString

```javascript
Object.prototype.toString({}) // '[object Object]'
Object.prototype.toString.call({}) // '[object Object]'
Object.prototype.toString.call(1) // '[object Number]'
Object.prototype.toString.call('1') // '[object String]'
Object.prototype.toString.call(true) // '[object Boolean]'
Object.prototype.toString.call(undefined) // '[object Undefined]'
Object.prototype.toString.call(null) // '[object Null]'
Object.prototype.toString.call(function(){}) // '[object Function]'
Object.prototype.toString.call(/123/g) // '[object RegExp]'
Object.prototype.toString.call(new Date()) // '[object Date]'
Object.prototype.toString.call([]) // '[object Array]'
Object.prototype.toString.call(document) // '[object HTMLDocument]'
Object.prototype.toString.call(window) // '[object Window]'
Object.prototype.toString.call(BigInt(1)) // '[object BigInt]'
```

`toString` is the prototype method of `Object`, by calling it we could get a formated answer for types, i.e. `[object Type]` (notice that every type is Capitalized). It's accurate enough for us to detect any data types.

Let's implement a type detecting method using `Object.prototype.toString` under the hood:

```javascript
// return the type in a capitalized string
function getType(value) {
	return Object.prototype.toString.call(value).replace(/^\[object (\S+)\]$/, '$1');
}
```

# Data Type Conversion

Watch out for the `==` equation operator! It would do implicitly type cast before comparison, and sometimes the result could be unexpected!

```javascript
'123' == 123 // true
'' == null // false
'' == 0 // true
[] == 0 // true
[] == '' // true
[] == ![] // true
null == undefined  // true
```

Therefore, we should **always use `===`** for comparison!

And some wired conversions:

```javascript
Number(null)  // 0
Number('')  // 0
parseInt('')  // NaN
Number({})  // NaN
{} + 10 // 10

let obj = {
  [Symbol.toPrimitive]() { return 200 },
  valueOf() { return 300 },
  toString() { return 'Hello' }
}
console.log(obj + 200)  // 400 (called [Symbol.toPrimitive] method)
```

## Type Casting Rules

Type Cast for primitives includes `Number()`, `parseInt()`, `parseFloat()`, `toString()`, `String()`, `Boolean()`.

And those wired results of `==` are all because of the rules of type cast here. So let's take a closer look.

### Rules of Number()

- For boolean, `true` and `false` would be converted to `1` and `0` respectively
- For number, return itself
- For null, return `0`
- For undefined, return `NaN`
- For string:
  - if the string only contains digits or hex that starts with `0x`  (sign is allowed), then coverts it to decimal number
  - if the string is in a valid format of float, then converts it to float number
  - if empty string, return `0`
  - else, return `NaN`
- For Symbol, throw Error
- For Object:
  - If there is `[Symbol.toPrimitive]` method, calls it and returns the result
  - else, call its `valueOf()` method, and follows the above rules to return the converted value
    - if the result is `NaN`, the call `toString()` method, again follows the above rules to return the converted value
  - return `NaN` if there is no other choice.

```JavaScript
Number(true)  // 1
Number(false)  // 0
Number('0111')  // 111
Number(null)  // 0
Number('')  // 0
Number('1a')  // NaN
Number(-0X11)  // -17
Number('0X11')  // 17
```

### Rules of Boolean()

Simple Rule: 

- if value is `undefined`, `null`, `false`, `''`, `0 (+0 and -0)`, `NaN`, return `false`
- else, return `true`

```
Boolean(0)  // false
Boolean(null)  // false
Boolean(undefined)  // false
Boolean(NaN)  // false
Boolean(1)  // true
Boolean(13)  // true
Boolean('')  // false
Boolean('12')  // true
```

## Implicit Type Cast

Regarding two values are not the same type, some operators would apply implicit type casting ahead of time, including logical Operators like (`&&`, `||`, `!`) , operators (`+`, `-`, `*`, `/`), relation operators (`>`, `<`, `<=`, `>=`), equality operator (`==`)  and condition `if` `while`.

### Rules of `==`

- if types are the same, no need to cast type
- if one of the value is `null` or `undefined`, return true if the other one is also `null` or `undefined`, otherwise false
- if one of them is `Symbol`, return false
- if the two values are  `string` and `number`, then `string` value would be converted to `number`
- if one of them is `boolean`, then converts to `number`
- if one of them is `object` and the other one is `string`, `number` or `symbol`, then firstly converts `object` to primitive type via  `valueOf` or `toString`.

### Rules of `+`

- if the two are `number`, sum them up and return the result
- if the two are `string`, concat them and return
- if one of them is `string` while the other is `undefined`, `null`, or `boolean`, then calls `toString` of the other value and concats the strings.
- if one of them is `number` while the other is `undefined`, `null`, or `boolean`, then converts the other to number and sum them up
- if the two values are `string` and `number`, then coverts number to string and concats them

```javascript
1 + 2 // 3
'1' + '2' // '12'
'1' + undefined // '1undefined'
'1' + null // '1null'
'1' + true // '1true'
'1' + 1n // '11'
1 + undefined // NaN
1 + null // 1
1 + true // 2
1 + 1n // error, cannot add number with BigInt  
'1' + 3 // '13'
```

### Rules of `Object`

- If there is `Symbol.toPrimitive`, call it and return result
- call `valueOf`, if the result is primitive, then return
- call `toString`, if the result is primitive, then return
- if none of the above result is primitive, throw error

```javascript
let obj = {
  value: 1,
  valueOf() {return 2},
  toString() {return '3'},
  [Symbol.toPrimitive]() {return 4}
}

console.log(obj + 1)  // 5
10 + {} // '10[object Object]' (for {}, call valueOf -> toString, then concat)
[1,2,undefined,4,5] + 10 // '1,2,,4,510'
```

# Summary

We've discussed three aspects of data structures in JavaScript:

1. The basics of data types
2. The common type detection methods
3. Type casting in JavaScript: be careful about the implicit casting!
