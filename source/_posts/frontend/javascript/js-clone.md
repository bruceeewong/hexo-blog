---
title: Shallow Clone vs Deep Clone in JavaScript
date: 2023-03-06 22:00:00
categories: [FrontEnd, JavaScript]
tags: [JavaScript, Clone]
---

In JavaScript programming, we would encounter many scenarios copying data to a new variable, i.e. data cloning. Because of the existence of reference data, we should definitely think about when to use `shallow clone` as well as `deep clone`. We are gonna talk about what are they, how do they work and implementation of them from scratch.

<!-- more -->

# Shallow Clone

A  `shallow clone` means that only the top-level properties of the original object are copied, and any nested objects or arrays are still shared by the original and the copy.

So if the property is a primitive, then the value would be copied as a new one; If it is a reference type (Object, Array, etc), then only address would be copied, modifying which would change the value of the new data.

Let's introduce some methods to do shallow clone.

## Method 1: object.assign

`object.assign` is an ES6 method in object. It merges the second and the trailing objects into the first object.

```javascript
let target = {};
Object.assign(target, { a: { b: 1 }, { c: 2 });
console.log(target); // { a: { b: 1 }, c: 2 };

```

Since it's a shallow clone, then modifying the  `b` value of `source`, which is inside object `a`, would affect the `target` object as well:

```javascript
let target = {};
let source = { a: { b: 2 } };
Object.assign(target, source);
console.log(target); // { a: { b: 10 } }; 
source.a.b = 10; 
console.log(source); // { a: { b: 10 } }; 
console.log(target); // { a: { b: 10 } };
```

> Tips: using object.assign
>
> - CAN NOT copy **inheritence properties**
> - CAN NOT copy **non-enumerable properties**
> - CAN copy **Symbol properties**

## Method 2: Spread Operator

```javascript
/* copy object */
let obj = {a:1,b:{c:1}}
let obj2 = {...obj}
obj.a = 2
console.log(obj)  //{a:2,b:{c:1}} console.log(obj2); //{a:1,b:{c:1}}
obj.b.c = 2
console.log(obj)  //{a:2,b:{c:2}} console.log(obj2); //{a:1,b:{c:2}}

/* copy array */
let arr = [1, 2, 3];
let newArr = [...arr]; // same as arr.slice()
```

## Method 3: Array Concat

Only for array's shallow clone:

```javascript
let arr = [1, 2, 3];
let newArr = arr.concat();
newArr[1] = 100;
console.log(arr);  // [ 1, 2, 3 ]
console.log(newArr); // [ 1, 100, 3 ]
```

## Method: Array Slice

Only for array's shallow clone:

```javascript
let arr = [1, 2, {val: 4}];
let newArr = arr.slice();
newArr[2].val = 1000;
console.log(arr);  //[ 1, 2, { val: 1000 } ]
```

## Try Implement a Shallow Clone

There are two ideas for implementing a shallow clone function:

1. For primitives, just copy it
2. For reference data, allocate a new space, and then copy the first level of its properties.

```javascript
function shallowClone(target) {
  if (typeof target === 'object' && target !== null) {
    const cloneTarget = Array.isArray(target) ? [] : {};
    // for..in loop can iterate both keys in object and indices in array
    for (let prop in target) {
      if (target.hasOwnProperty(prop)) {
        cloneTarget[prop] = target[prop];
      }
    }
    return cloneTarget;
  }
  return target;
}
```

# Deep Clone

Instead of just copying the first level of properties, a `deep clone` copies data in depth, which completely separate the storage of the data in memory.

There are also a few methods to achieve deep clone.

## Method 1: JSON.stringify + JSON.parse

This is the simplest way to do deep clone in practice. It converts an object to a JSON string, which we can use `JSON.parse` to recover it as a brand new object.

```javascript
let obj1 = { a:1, b:[1,2,3] }
let str = JSON.stringify(obj1)；
let obj2 = JSON.parse(str)；
console.log(obj2);   //{a:1,b:[1,2,3]} 

obj1.a = 2；
obj1.b.push(4);
console.log(obj1);   //{a:2,b:[1,2,3,4]}
console.log(obj2);   //{a:1,b:[1,2,3]}
```

However, there are some limitations of this method after a conversion:

1. CAN NOT copy: `function`, `undefined`, `symbol`
2. CAN NOT copy non-enumerable properties
3. CAN NOT copy prototype chain
4. CAN NOT copy circular references, i.e. `obj[key] = obj`
5. `Date` value would become `string`
6. `RegExp` value would become `{}`
7. `NaN`, `+-Infinity` value would become `null`

Here is a code example:

```javascript
function Obj() { 
  this.func = function () { alert(1) }; 
  this.obj = {a:1};
  this.arr = [1,2,3];
  this.und = undefined; 
  this.reg = /123/; 
  this.date = new Date(0); 
  this.NaN = NaN;
  this.infinity = Infinity;
  this.sym = Symbol(1);
} 

let obj1 = new Obj();
Object.defineProperty(obj1,'nonenumerable',{ 
  enumerable:false,
  value:'a non-enumerable value'
});
console.log('obj1',obj1);
let str = JSON.stringify(obj1);
let obj2 = JSON.parse(str);
console.log('obj2',obj2);
```

![image-20230306223956192](https://static.bruski.wang/picgo/20230306223958-43a1feb4a6830b68b8c32be1e74abaf5.png)



## Method 2: Basic Recursion Implementation

Use `for..in` loop to iterate properties: 

- if it is a reference type, recursively call the clone
- else, copy it

```javascript
function deepClone(target) {
  let cloneObj = {}
  for (let key in target) {
    if (typeof target[key] === 'object') {
      cloneObj[key] = deepClone(target[key]);
    } else {
      cloneObj[key] = target[key];
    }
  }
  return cloneObj;
}
```

For this basic version, we still cannot deal with:

- Non-enumerable and symbol properties
- Array, Date, RegExp, Error, Function properties
- Circular reference properties

## Method 3: Advanced Implementation

Regarding the above shortages, we can solve them in these ways:

1. For non-enumerable and symbol properties, we can use `Reflect.ownKeys` to iterator them
2. For `Date`, `RegExp` properties, we create new instances based on them
3. For prototype chain, we can use `Object.getOwnPropertyDescriptors` to get every property, attribute on the object; Along with `Object.create` to create a new object, we can pass the prototype chain of the original object to inherit it.
4. For circular references, using `WeakMap` as hash table can detect them as well as avoiding memory leak.

> WeakMap is a special kind of Map whose keys must be objects, and it does not prevent its keys from being garbage.

Now let's take a look at the advanced version:

```javascript
function deepClone(obj, map = new WeakMap()) {
  // base cases
  if (['number', 'string', 'boolean', 'bigint', 'symbol'].includes(typeof obj)) {
    return obj;
  }
  if (obj === null) return null;
  if (obj.constructor == Date) return new Date(obj);
  if (obj.constructor == RegExp) return new RegExp(obj);
  if (map.has(obj)) return map.get(obj);  // circular reference detected
  
  let allDesc = Object.getOwnPropertyDescriptors(obj);
  let cloneObj = Object.create(Object.getPrototypeOf(obj), allDesc);  // inherit prototype
  map.set(obj, cloneObj);  // for detecting circular reference
  
  for (let key of Reflect.ownKeys(obj)) {
   	if (typeof obj[key] === 'object' && obj[key] !== null) {
      cloneObj[key] = deepClone(obj[key], map);
    } else {
      cloneObj[key] = obj[key];
    }
  }
  return cloneObj;
}
```

Test this complex function:

```javascript
let obj = {
  num: 0,
  str: '',
  boolean: true,
  unf: undefined,
  nul: null,
  obj: { name: 'aaa', id: 1 },
  arr: [0, 1, 2],
  func: function () { console.log('bbb') },
  date: new Date(0),
  reg: new RegExp('/ccc/ig'),
  [Symbol('1')]: 1,
};
Object.defineProperty(obj, 'nonenumerable', {
  enumerable: false, value: 'a non-enumerable value' }
);
obj = Object.create(obj, Object.getOwnPropertyDescriptors(obj))
obj.loop = obj    // set circular reference
let cloneObj = deepClone(obj)
cloneObj.arr.push(4)

console.log('obj', obj)
console.log('cloneObj', cloneObj)
```

![image-20230306230446676](/Users/bruski/Library/Application Support/typora-user-images/image-20230306230446676.png)

The resule shows we have done the deep clone perfectly🔥

# Summary

Getting familiar with shallow clone and deep clone is crucial for javascript programming. By implementing them, there are many knowledge have been tested:

- Basic coding skills
  - Recursion
  - Preciseness
  - Abstraction
- JS coding skills
  - Type checking
  - APIs of reference types
  - WeakMap
- Comprehensive skills
  - Edge cases
  - Circular reference detection

See, we've come so far!

That's all for today's topic. Hope these clone stuffs become clear to you!

