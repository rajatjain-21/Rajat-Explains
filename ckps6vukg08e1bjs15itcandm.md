## Arrow vs Regular functions

If you like to listen 🎧 to this article as a natural sounding 🔈 audio experience:

%%[arrow-vs-regular-function-audio]
Most of us know that the usual way of declaring/defining functions in most languages is a simple regular syntax. In Javascript too, for a long time you could declare functions only using this syntax
```javascript
// Function Declaration
function goodOlDays(whatever) {
  console.log(`Hello ${ whatever }`)
}

// Function Expression
const goodOldDays = function(whatever) {
  console.log(`Hello ${ whatever }`)
}
```
I will call the above declared or expressed function as the regular function.

Then, with ES6 (ES2015), there came a new unusual syntax, something of a sort that was quite unseen in any other language and was aesthetically cooler.
```javascript
// Arrow function syntax
const newerSyntax = (whatever) => {
  console.log(`Hello ${ whatever }`)
}
```
This did not mean that the old syntax was not allowed or valid anymore. Actually, there are times when using the regular syntax actually is the easier/right path. While both the syntaxes define functions, when would you choose one over another? That’s a good question.

In this post, I’ll show the main differences between the two, so you could choose the right syntax as per your needs.

## 1. The ***this*** value
To understand in depth how `this` works in JS, I have a nice descriptive [article](https://rajatexplains.com/this-in-javascript).
![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1623401474123/rOoD1qoQN.png)
You can check it out.

### a). Regular functions
In regular functions, the `this` value (aka **execution context**) is dynamic, i.e., **its value depends on how the function is invoked**. In Javascript, there are four ways in which a function can be invoked.

👉 **Simple invocation**

During a simple invocation the value of `this` equals to the `global` object (or undefined if the function runs in `strict` mode):
```javascript
function doSomething() {
  console.log(this)
}

// Simple invocation
doSomething(); // logs global object (window)
```
👉 **Method invocation**
> 
A **method**, like a **function**, is a set of instructions that performs a task. The difference is that a **method** is associated with an object, while a **function** is not.

In method invocation, the value of `this` is the object owning the method
```javascript
const myObj = {
  doSomething() {
    console.log(this)
  }
}

//Method invocation
myObj.doSomething() // logs myObj
```
👉 **Indirect invocation**

In this, the function is invoked using the `call` or `apply` method and the value of `this` is passed **explicitly** and is equal to the first argument
```javascript
function doSomething() {
  console.log(this);
}

const context = { value: 'A' };

doSomething.call(context);  // logs { value: 'A' }
doSomething.apply(context); // logs { value: 'A' }
```
👉 **Constructor invocation**

During a constructor invocation using `new` keyword, `this` is equal to the newly created instance:
```javascript
function DoSomething() {
  console.log(this);
}

new DoSomething(); // logs an instance of DoSomething
```

### b). Arrow Functions
The behavior of `this` inside of an arrow function differs considerably from the regular function’s `this` behavior. **The arrow function doesn’t define its own execution context**.

No matter how or where being executed, `this` value inside of an arrow function always equals `this` value from the outer function. In other words, **the arrow function resolves `this` lexically**.
```javascript
const doSomething = {
  myMethod() {
    console.log(this); // logs myObject
    const callback = () => {
      console.log(this); // logs myObject
    };
    callback();
  }
};

doSomething.myMethod();
```
Here, callback does not have its own `this`. It "looks up" (resolves lexically) in its outer context and assumes the value of `this` from there.

`this` resolved lexically is one of the great features of arrow functions. 
When using callbacks inside methods you are sure the **arrow function doesn’t define its own `this`**: no more const `self = this or callback.bind(this)` workarounds.

Contrary to a regular function, the indirect invocation of an arrow function using `myArrowFunc.call(thisVal)` or `myArrowFunc.apply(thisVal)` doesn’t change the value of `this`: **the context value is always resolved lexically**.


## 2. Constructors
### a). Regular functions
As seen in the previous section, the regular function can easily construct objects.
```javascript
function Human(feature) {
  this.feature = feature;
}

const rajat = new Human("explains");
rajat instanceof Human; // => true 
```
### b). Arrow functions
But, in case of an arrow function, the constructor invocation throws `TypeError`. Because of the fact that `this` in arrow function is resolved lexically, it can't be invoked with the *new* keyword
```javascript
const Human = (feature) => {
  this.feature = feature;
}

const rajat = new Human("explains") // TypeError: Human is not a constructor
```
(pun intended in the error 😛)

## 3. *arguments* object
### a). Regular functions
In a regular function, you do have an `arguments` object(array-like) out of the box, which you can directly consume. `arguments` is a special array-like object containing the list of arguments with which the function has been invoked.
```javascript
function myFunction() {
  console.log(arguments);
}

myFunction('a', 'b'); // logs { 0: 'a', 1: 'b', length: 2 }
```

### b). Arrow functions
In an arrow function, there is no special keyword as `arguments`. 
However, like the `this` value, you can have `arguments` lexically resolved
```javascript
function myRegularFunction() {
  const myArrowFunction = () => {
    console.log(arguments);
  }

  myArrowFunction('c', 'd');
}

myRegularFunction('a', 'b'); // logs { 0: 'a', 1: 'b', length: 2 }
```
To get the arguments passed to an arrow function, there is a nice little trick that you can follow or may already be following unknowingly, underestimating its power.
```javascript
const myArrowFunction = (...args) => {
  console.log(args)
}
myArrowFunction(1,2,3) // logs [1, 2, 3]
```
It assumes that the arguments are passed as some array, which has been spread out.
**Beautiful, isn't it!** ❤️



# Bonus
## ❓Question Time ⏳
Do you know, why in case of **regular** callback methods or async methods, for consuming `this`, you have to explicitly use `bind` function.
For example - inside Class component in React, you must be seeing this pattern quite often, where you have to `bind` your regularly declared callback method in the constructor with the context of the class.
What is the reason? Can you explain it well enough to yourself or to another person in words?

Let me help you there. 🚀🚀

The regular functions are the usual way to define methods on classes.

In the following class `Human`, the method `logName()` is defined using a regular function:
```javascript
class Human {
  constructor(name) {
    this.name = name;
  }

  logName() {
    console.log(this.name);
  }
}

const rajat = new Human('rajat');
```
Usually, regular functions are a way to go. Now, here if you do `console.log(rajat.logName()), `rajat` will be logged in console.

But, sometimes, you need to pass the function inside a setTimeout as a callback and you wait for it to be called after sometime.
```javascript
setTimeout(rajat.logName, 1000);
// after 1 second logs "undefined"
```
This is a classic case, where you are passing a function reference as a callback. Consider it like a **detached function body**, which will now have no association with the class or object it was enclosed in while declaration. Now, after 1s, when the function will be called, the value of `this` will now be the window object.

**To prevent this from happening, you can do two things:**

1. You can `bind` the context explicitly while passing to the `setTimeout`
```javascript
setTimeout(rajat.logName.bind(rajat), 1000);
// after 1 second logs "rajat"
```
`rajat.logName.bind(rajat)` binds this value to `rajat` instance. Now you’re sure that the method doesn’t lose the context.
There’s a better way: **the arrow functions as a class field**.

1. You can use arrow functions as methods inside class. So, instead of the class code above

```javascript
class Human {
  constructor(name) {
    this.name = name;
  }

  logName = () => {
    console.log(this.name);
  }
}

const rajat = new Human("rajat")
```
Now you can use `rajat.logName` as a callback **without any manual binding** of this. The value of this inside `logName()` method is always the class instance, because as you might remember from the sections above, 

**a.)** arrow functions don't have their own `this`.

**b.) **`this` inside arrow function is always resolved lexically.

So, it will always be the class instance

```javascript
setTimeout(rajat.logName, 1000);
// after 1 second logs "rajat"
```


## TL;DR

- Understanding the differences between regular and arrow functions helps choose the right syntax for specific needs.

- `this` value inside a regular function is dynamic and depends on the invocation. But `this` inside the arrow function is bound lexically and equals to `this` of the outer function.

- `arguments` object inside the regular functions contains the list of arguments. The arrow function, on the opposite, doesn’t define `arguments` (but you can easily access the arrow function arguments using a rest parameter `...args`).

- Last but not the least, you can define methods using the arrow function syntax inside classes. Fat arrow methods bind this value to the class instance. Anyhow the fat arrow method is invoked, this always equals the class instance, which is useful when the methods are used as callbacks.


Thanks for reading 🙏

If you liked what you read 🧑‍🏫 and got to learn new things, do hit like 👍  and don't forget to follow 🚀 me on

Hashnode - @[Rajat Jain](@rajatexplains)

Twitter - [@rajat_codes](https://twitter.com/rajat_codes)

Instagram - [@javascript_to_the_rescue](https://instagram.com/javascript_to_the_rescue)

LinkedIn - [Rajat Jain](https://www.linkedin.com/in/rajatjain-21/)



