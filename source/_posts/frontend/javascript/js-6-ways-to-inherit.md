---
title: 6 Ways of Inheritance in JavaScript
date: 2023-03-08 19:45:00
categories: [FrontEnd, JavaScript]
tags: [JavaScript, Data Structure]
---

This article is going to talk about the `Inheritance` in JavaScript.

Inheritance is a concept belonging to Object-Oriented Programming (OOP), using which we can have a elegent way to reuse codes and manage entity relationships.

Before we start, let me ask you a couple of questions:

1. How many ways for inheritance are there in JavaScript?
2. What's under the hood of ES6 keyword `extends`?

If you seem to feel hesitate about them, then don't hesitate to walk through with me.

<!-- more -->

# 6 Ways for interitance (ES5)

## Method 1: Prototype Chain Interitance

Prototype chain inheritance is one of the most common way in JavaScript. It includes `constructor`, `prototype` and `instance`. The relationships among them are:

- Every `constructor function` has a `prototype object`
- Every `prototype object` contains a pointer to its `constructor function`
- Every instance contains a pointer to its `prototype object`

![image-20230308195607698](https://static.bruski.wang/picgo/20230308195609-da5d7db9ce954cce7dc7e0e2d8857efa.png)

Let's watch a demo for this:

```javascript
  function Parent1() {
    this.name = 'parent1';
    this.play = [1, 2, 3]
  }

  function Child1() {
    this.type = 'child2';
  }

  Child1.prototype = new Parent1();  // chain the prototype to a Parent1 object
  console.log(new Child1());

```

It has a severe problem:

```javascript
  var s1 = new Child1();
  var s2 = new Child2();

  s1.play.push(4);
  console.log(s1.play, s2.play);  // [1,2,3,4], both affected
```

Why is this happening? Because **the two instances use the same prototype**. It's one of the biggest drawbacks for `Prototype Chain Inheritance`.

So let's move on to the next method that helps with this issue.

## Method 2: Constructor Inheritance (by using call)

Watch this demo:

```javascript
  function Parent1(){
    this.name = 'parent1';
    this.play = [1, 2, 3];
  }

  Parent1.prototype.getName = function () {
    return this.name;
  }

  function Child1(){
    Parent1.call(this);
    this.type = 'child1'
  }

  let child1 = new Child1();
  let child2 = new Child1();
	child1.play.push(4);
	console.log(child1.play, child2.play); // [1,2,3,4] for child1, [1,2,3] for child 2, problem solved
  console.log(child1.getName());  // throws error
```

Now we solve the problem of the method one, i.e. we get the properties from the parent. However, we lose the prototype functions of the parent too.

So this method only allows us to inherit the instance properties and functions from parents instead of prototypal ones.

What if we combine the above two worlds?

## Method 3: Combination Inheritance

We combine the prototype inheritance as well as the constructor inheritance, and here we go:

```javascript
  function Parent3 () {
    this.name = 'parent3';
    this.play = [1, 2, 3];
  }

  Parent3.prototype.getName = function () {
    return this.name;
  }

  function Child3() {
    Parent3.call(this);  // second time to call Parent3
    this.type = 'child3';
  }

	// Constructor -> protoype
  Child3.prototype = new Parent3();  // first time to call Parent3
	// prototype -> constructor
  Child3.prototype.constructor = Child3;

  var s3 = new Child3();
  var s4 = new Child3();

  s3.play.push(4);
  console.log(s3.play, s4.play);  // [1,2,3,4], [1,2,3] (do not affect to each other)
  console.log(s3.getName()); // 'parent3'
  console.log(s4.getName()); // 'parent3'
```

Now we're getting things done, but not perfectly. See we called Parent 3 twice? The first time is to change Child3's protoype while the other time is within the constructor function `Child3`. This may have performance concerns, which we definitly want to avoid.

We will leave this question to the Six method. Before that, we are going to introduce some inheritance ways using `object` rather than `constructor function`.

## Method 4: Prototypal Inheritance

`Prototypal inheritance` is a way of creating an object that inherits the properties and methods of **another object**, using the **prototype** object.

Watch out ladies and gentlemen, we are inheriting straightly from an object! How does the magic work?

We got to mention an ES5 method called `Object.create`, which takes up two params: the first one is for the model object, the second one is for the object that defines additional properties for our new born object.

Take a look at the example of how we implement inheritance from an object:

```javascript
  let parent4 = {
    name: "parent4",
    friends: ["p1", "p2", "p3"],
    getName: function() {
      return this.name;
    }
  };

  let person4 = Object.create(parent4);
  person4.name = "tom";
  person4.friends.push("jerry");

  let person5 = Object.create(parent4);
  person5.friends.push("lucy");

  console.log(person4.name);  // 'tom'
  console.log(person4.name === person4.getName());  // true
  console.log(person5.name);  // 'parent4'
  console.log(person4.friends);  // ['p1', 'p2', 'p3', 'jerry', 'lucy']
  console.log(person5.friends);  // ['p1', 'p2', 'p3', 'jerry', 'lucy']
```

We see that `Object.create` enables our new objects to inherit the properties from `parent4`. But we do see it has the same reference problem here, a.k.a shallow cloning.

## Method 5: Parasitic Inheritance

`Parasitic Inheritance` does not solve the issues as `Prototypal Inheritance` has, but it adds some own functions beyond the shallow cloning from parents.

```javascript
   let parent5 = {
    name: "parent5",
    friends: ["p1", "p2", "p3"],
    getName: function() {
      return this.name;
    }
  };

  function clone(original) {
    let clone = Object.create(original);
    // add own functions
    clone.getFriends = function() {
      return this.friends;
    };
    return clone;
  }

  let person5 = clone(parent5);
  console.log(person5.getName());
  console.log(person5.getFriends());

```

Adding more functions rather than just copying from parent objects, that's parasitic inheritance.

## Method 6: Parasitic Combination Inheritance

Concluding all the methods above, we get a relatively better solution.

```javascript
function clone (parent, child) {
  // using object.create, we reduce a new call for parent
  child.prototype = Object.create(parent.prototype);  // prototypal inheritance
  child.prototype.constructor = child;  // combination inheritance
}

function Parent6() {
  this.name = 'parent6';
  this.play = [1, 2, 3];
}
 Parent6.prototype.getName = function () {
  return this.name;
}

function Child6() {
  Parent6.call(this);  // constructor inheritance
  this.friends = 'child5';
}
clone(Parent6, Child6); // parasitic inheritance
Child6.prototype.getFriends = function () {
  return this.friends;
}

let person6 = new Child6();
console.log(person6);
console.log(person6.getName()); // 'parent6'
console.log(person6.getFriends());  // 'child5'
```

![image-20230308204059292](https://static.bruski.wang/picgo/20230308204101-5db288177735ade8131721622d030100.png)



Regarding the result, we are getting the properties and functions properly for the child from the parent.

In a nutshell, we finally find out a good way for inheritance in JavaScript, which considers:

- Inheritance of parent's instance properties without the same reference issue.
-  Inheritance of parent's prototype functions
- Only once call for parent constructor

And after ES6 came out, there is an offical way to do it!

# Method 7: Extends (ES6)

```javascript
class Person {
  constructor(name) {
    this.name = name
  }
  // prototypal methods
  // <=> Person.prototype.getName = function() { }
  getName() {
    console.log('Person:', this.name)
  }
}

class Gamer extends Person {
  constructor(name, age) {
    super(name) // call super to inherit from the parent
    this.age = age
  }
}
const asuna = new Gamer('Asuna', 20)
asuna.getName() // easy to use methods from parent
```

Things just go smooth because of the occurance of `class ` and `extends` . But there are compatiblity issues for old versions of browsers, if we want the above ES6 syntax work on those browsers, we need a transformer called `Babel` to compile ES6 to ES5.

Now we're gonna uncover the real face under ES6 syntax `class` and `extends`, which inheritance method does it use?

```javascript
function _possibleConstructorReturn (self, call) { 
		// ...
		return call && (typeof call === 'object' || typeof call === 'function') ? call : self; 
}

// we see parasitic combination inheritance here!
function _inherits (subClass, superClass) { 
	subClass.prototype = Object.create(superClass && superClass.prototype, { 
		constructor: { 
			value: subClass, 
			enumerable: false, 
			writable: true, 
			configurable: true 
		} 
	}); 
	if (superClass) Object.setPrototypeOf ? Object.setPrototypeOf(subClass, superClass) : subClass.__proto__ = superClass; 
}

var Parent = function Parent () {
	_classCallCheck(this, Parent);
};

var Child = (function (_Parent) {
	_inherits(Child, _Parent);
	function Child () {
		_classCallCheck(this, Child);
    // Same as Parent.call(this)
		return _possibleConstructorReturn(this, (Child.__proto__ || Object.getPrototypeOf(Child)).apply(this, arguments));
}
	return Child;
}(Parent));
```

Wow, we see `parasitic combination inheritance` under the hood, that means it is a good way to do inheritance in JavaScript!

# Summary

We've seen 6 ways to do inheritance and their pros and cons, and we've also uncover the mystery of ES6 extends. Take the below mind graph as reviewing:

![image-20230308210103184](https://static.bruski.wang/picgo/20230308210105-8b9203192d276b03ffbed5c504b66476.png)

Divied by using `object.create` or not, we finally pick Parasitic Combination Inheritance as our preference.

What a journey, see you next time!
