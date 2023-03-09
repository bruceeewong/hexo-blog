---
title: New, Apply, Call, Bind in JavaScript
date: 2023-03-08 22:40:00
categories: [FrontEnd, JavaScript]
tags: [JavaScript, Function]
---

In JavaScript, `apply`, `call`, `bind` are crucial concepts for development, which all relate closely with `this` pointer.

I'm gonna start with a few questions:

- What does `new` actually do?
- Difference among `apply`, `call` and `bind` ?
- How to implement an `apply ` or `call`?

Well, let's get started!

<!-- more -->

# New

The main function of  `new`  is to execute constructor functions and return a new instance.

Let's check out a demo:

```javascript
function Person(){
   this.name = 'Jack';
}

var p = new Person(); 
console.log(p.name)  // Jack
```

Easy enough, we call `new` on constructor `Person` and get a new instance of Person.

Let's break down what `new` had done during the above process:

1. Creates a new object
2. Assign the scope of the constructor to the new object (or say `this` binding) 
3. Execute the constructor function
4. return the new instance

What happen if we don't use `new` for the same process? Let's try.

```javascript
function Person(){
  this.name = 'Jack';
}

var p = Person();
console.log(p) // undefined
console.log(name) // Jack
console.log(p.name) // 'name' of undefined
```

See? We don't get a new object back `p`. Instead, the `this` during the execution points to the global `window` object by default, which allows us to call `name` without explicitly declaring it.

One step further, if the constructor returns a object, how would things change with `new`?

```javascript
function Person(){
   this.name = 'Jack'; 
   return {age: 18}
}

var p = new Person(); 

console.log(p)  // {age: 18}
console.log(p.name) // undefined
console.log(p.age) // 18
```

Obviously, `new` doesn't create a new object. It returns the object straightly.

But, if the constructor returns non-object value, `new` works as usual.

```javascript
function Person(){
   this.name = 'Jack'; 
   return 'tom';
}
var p = new Person(); 

console.log(p)  // {name: 'Jack'}
console.log(p.name) // Jack
```

Therefore, we can conclude that after `new` we always receive a object, either new instance or object that returned by the constructor.

# Apply & Call & Bind

`apply`, `call`, `bind` are three methods of `function`. Let's take a look at a demo:

```javascript
func.call(thisArg, param1, param2, ...)
func.apply(thisArg, [param1,param2,...])
func.bind(thisArg, param1, param2, ...)
```

The common usage of these three is to change the `this` of the `function`, i.e. the execution subject. `thisArg` refers to the `this` object you want to call on.

Difference for the second param among them:

- `apply` receives an param array
- `call` and `bind` bypasses param from position 2 to N
- `bind` would not execute the function immediately, it would return a new function with a changed `this` .

What are all these for? **Reusage**! Say, we don't have to buy a house for living if we can rent one. Same here, we borrows functions from existing objects, saving spaces and efforts.

Let's take a deeper look for the usage and everything should be clear:

```javascript
let a = {
  name: 'jack',
  getName: function(msg) {
    return msg + this.name;
  }
}

let b = {
  name: 'lily'
}

console.log(a.getName('hello~'));  // hello~jack
console.log(a.getName.call(b, 'hi~'));  // hi~lily
console.log(a.getName.apply(b, ['hi~']))  // hi~lily
let name = a.getName.bind(b, 'hello~');
console.log(name());  // hello~lily
```

# Classic scenarios

Let's see what we can do with these three **BORROW** ablities.

## Type Checking

Remember how we use `Object.prototype.toString` for checking variable types. There it is.

```javascript
function getType(obj){
  return Object.prototype.toString.call(obj);
}
```

## Array-Like Value

We can have an array-like value if we define our object as:

```javascript
var arrayLike = { 
  0: 'java',
  1: 'script',
  length: 2
} 
```

But sure it has no array methods. No worry, we can borrow from real Array!

```javascript
Array.prototype.push.call(arrayLike, 'jack', 'lily'); 
console.log(typeof arrayLike); // 'object'
console.log(arrayLike);
// {0: "java", 1: "script", 2: "jack", 3: "lily", length: 4}
```

We just pushed a new value into the array-like object by calling Array's push! Isn't that magical?

## Get max/min of an array

Check this out: we can pass an array and get its max/min value by using `Math.max`/`Math.min`:

```javascript
let arr = [13, 6, 10, 11, 16];
const max = Math.max.apply(Math, arr); 
const min = Math.min.apply(Math, arr);

console.log(max);  // 16
console.log(min);  // 6
```

## Inheritance

Back in the previous article [6 Ways of Inheritance in JavaScript](https://blog.bruski.wang/2023/03/08/frontend/javascript/js-6-ways-to-inherit/#Method-2-Constructor-Inheritance-by-using-call), we use `Parent.call(this)` inside a child constructor for inheriting the properties from parents. 

# Can we implement then on our own?

## Try `new`

Let's do a quick recap about what does `new` do during execution:

1. Allows instances to visit private properties
2. Allows instances to visit the properties on constructor's prototype chain. (`constructor.prototype`)
3. Return a reference type data (either new instance or result of constructor)

Here is our version:

```javascript
function _new(ctor, ...args) {
  if (typeof ctor !== 'function') {
    throw new Error('ctor must be a function');
  }
  let object = new Object();
  obj.__proto__ = Object.create(ctor.prototype);  // assign prototype to the instance
  let res = ctor.apply(obj, [...args]);
  
  let isObject = typeof res === 'object' && res !== null;
  let isFunction = typeof res == 'function';
  return isObject || isFunction ? res : object;
}
```

## Try `apply` and `call`

The principle of `apply` and `call` is similar, let's implement them together!

```javascript
Function.prototype.call = function (context, ...args) {
  let context = context || window;
  context.fn = this;  // bind this function to the new context
  let result = eval('context.fn(...args)');
  delete context.fn;
  return result;
}

Function.prototype.apply = function (context, args) {
  let context = context || window;
  context.fn = this;  // bind this function to the new context
  let result = eval('context.fn(...args)');
  delete context.fn;
  return result;
}
```

The key is to use `eval` to execute the function itself in a different context (or say on a different object).

## Try `bind`

The basic principle of `bind` is also similar, but `bind` doesn't execute immediately, so we don't need `eval` anymore.

```javascript
Function.prototypeFunction.prototype.bind = function (context, ...args) {
  if (typeof this !== 'function') {
    throw new Error('this must be a function');
  }
  let self = this;
  let fbound = function(...fargs) {
    self.apply(this instanceof self ? this : context, args.concat(fargs));
  }
  // should not lose current `this`'s prototype 
  if (this.prototype) {
    fbound.prototype = Object.creaet(this.prototype);
  }
  return fbound;
}
```

The key here is to return a function with a changed context. Also we can't lose the prototype in the new function.

# Sumamry

Let's do a summary for the three methods:

| Method         | call                         | apply                        | Bind                         |
| -------------- | ---------------------------- | ---------------------------- | ---------------------------- |
| params         | multiple                     | one array                    | multiple                     |
| function       | change `this` for a function | change `this` for a function | change `this` for a function |
| result         | immediate execution          | immediate execution          | return a function            |
| implementation | call `eval`                  | call `eval`                  | indirectly call `apply`      |
