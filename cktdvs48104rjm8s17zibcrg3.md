## Easy Implementation of Promise in JS

<mark>**Disclaimer**: In this post, I am not going to implement Promise exactly the way it has been done in the JS specification. This is more likely a frontend interview question and focusses more on the functional aspect of Promise.</mark>

You can find the complete code  [here](https://gist.github.com/rajatjain-21/339e1c52c5f957499ef73973da5a4236). To more of my code gists, checkout my  [Github Gists](https://rajatexplains.com/rajatjain-21) 

When I started preparing for my interviews, I started looking for ways to implement my own Promise class (as this was a commonly asked question in good interviews). There are several places where people have implemented it, but, with all due respect, I didn't find a single one of them easy to wrap my head around. Furthermore, when you are asked to implement it in an interview, you need to come up with something that **you can keep in your head and that is not a lengthy implementation**. So, having read most of those blog posts and clearing my understanding about promises, I came up this **simple** implementation.

So, let's get started 💪 🚀

## 🧠 Things we know about the Promise class
These are the points that we'll base our Promise implementation on:

1. When we declare a promise with the `new Promise(executor)` syntax, the executor function passed is called then and there with two arguments (`resolve` and `reject`).
```javascript
const promise = new Promise((resolve, reject) => {
  console.log(42)
})
// a new promise is created, but at the same time 
// 42 is logged to console.
```
1. The value that the promise will resolve to and that we will be able to access it using the `then` method, depends on what value is `resolve` function (which is one of the arguments of the executor fn) is called with.
```javascript
const promise = new Promise((resolve, reject) => {
  console.log("Inside Promise executor"); 
  resolve(42);
})
promise.then(value => console.log(value)); 
// 42 is logged to console.
```
"Inside Promise executor" will immediately get logged to the console as soon as the promise is created. It's only when `then` method is called, we are able to get the resolved value and the respective callback function runs.

1. A promise is considered `pending` by default till the time `resolve` or `reject` method is not called. Once, `resolve` is called, it is considered as `fulfilled` or in case of `reject`, it is considered as `rejected`.

We will start constructing our `Promise` function now.

💡 **Why function and not a class?**

**A class in JS is a syntactic sugar and is internally implemented as a function only.**
```javascript
class DummyClass {
  constructor() {}
  ...
  ...
}

typeof DummyClass // "function"
```
Let us start with the implementation step by step. We will call it `MyPromise`.

## 🧑‍💻 Code Implementation

```javascript
function MyPromise(executor) {
  let onResolve, onReject;
  let fulfilled = false,
    rejected = false,
    called = false,
    value;
}
```
- `executor` function will be of the form `function(resolve, reject) {}`
- `onResolve` and `onReject` are the callbacks passed on promise resolution or rejection.
- initially all these flags `fulfilled`, `rejected`, `called` values will be false.
- `fulfilled` or `rejected` will be made true when `resolve` or `reject` methods are called respectively.
- `called` will be made true as soon as soon the onResolve or onReject callback is called, i.e., when `then` will be called.
- `value` will be assigned with the resolved value or rejected reason.

Let us write our `resolve` and `reject` methods now

```javascript
function resolve(v) {
  fulfilled = true;
  value = v;
  if (typeof onResolve === "function") {
    // will come inside this when say, resolve is called inside a timeout
    onResolve(value);
    called = true;
  }
}

function reject(reason) {
  rejected = true;
  value = reason;
  if (typeof onReject === "function") {
    // will come inside this when say, reject is called inside a timeout
    onReject(value);
    called = true;
  }
}
```

Now, let us move onto writing our `then` and `catch` methods. Theses are both prototype methods on MyPromise.
```javascript
this.then = function(callback) {
  onResolve = callback;
  if (fulfilled && !called) {
    called = true;
    onResolve(value);
  }
  return this;
}
  
this.catch = function (callback) {
  onReject = callback;
  if (rejected && !called) {
    called = true;
    onReject(value);
  }
  return this;
};
```
- `onResolve` variable is assigned with the callback that is passed to the `then` method, similarly for `onReject` variable.
- `called` value is made true.
- As `then` and `catch` should also return a Promise (so that we can again call `then` or `catch` on it), we return `this` instance.
> Actually, a `Thenable` function is returned from `then`, and not technically a `Promise`. So, any function that has got a then and a catch prototype method can be returned from `then`.

Let us now, complete our `MyPromise` function
```javascript
try {
  executor(resolve, reject);
} catch (error) {
  reject(error);
}
```

Let us also write some static methods on `MyPromise`
```javascript
// resolve method
MyPromise.resolve = (val) =>
  new MyPromise(function executor(resolve, _reject) {
    resolve(val);
  });

// reject method
MyPromise.reject = (reason) =>
  new MyPromise(function executor(resolve, reject) {
    reject(reason);
  });

// Promise.all implementation
MyPromise.all = (promises) => {
  let fulfilledPromises = [],
    result = [];

  function executor(resolve, reject) {
    promises.forEach((promise, index) =>
      promise
        .then((val) => {
          fulfilledPromises.push(true);
          result[index] = val;

          if (fulfilledPromises.length === promises.length) {
            return resolve(result);
          }
        })
        .catch((error) => {
          return reject(error);
        })
    );
  }
  return new MyPromise(executor);
};
```
Checkout my blog post on `Promise.all` with detailed explanation.

%[https://rajatexplains.com/promise-all]


## 🔥 Complete Implementation of MyPromise
```javascript
function MyPromise(executor) {
  let onResolve, onReject;
  let fulfilled = false,
    rejected = false,
    called = false,
    value;

  function resolve(v) {
    fulfilled = true;
    value = v;
    if (typeof onResolve === "function") {
      onResolve(value);
      called = true;
    }
  }

  function reject(reason) {
    rejected = true;
    value = reason;
    if (typeof onReject === "function") {
      onReject(value);
      called = true;
    }
  }
  
  this.then = function(callback) {
  	onResolve = callback;
    if (fulfilled && !called) {
    	called = true;
      onResolve(value);
    }
  	return this;
  }
  
  this.catch = function (callback) {
    onReject = callback;
    if (rejected && !called) {
      called = true;
      onReject(value);
    }
    return this;
  };
  
  try {
    executor(resolve, reject);
  } catch (error) {
    reject(error);
  }
}

MyPromise.resolve = (val) =>
  new MyPromise(function executor(resolve, _reject) {
    resolve(val);
  });

MyPromise.reject = (reason) =>
  new MyPromise(function executor(resolve, reject) {
    reject(reason);
  });

MyPromise.all = (promises) => {
  let fulfilledPromises = [],
    result = [];

  function executor(resolve, reject) {
    promises.forEach((promise, index) =>
      promise
        .then((val) => {
          fulfilledPromises.push(true);
          result[index] = val;

          if (fulfilledPromises.length === promises.length) {
            return resolve(result);
          }
        })
        .catch((error) => {
          return reject(error);
        })
    );
  }
  return new MyPromise(executor);
};

const promise = new MyPromise((resolve, reject) => {
	console.log("Rajat");
	setTimeout(() => resolve(42), 100)
})

promise
.then(val => console.log(val))
.catch(error => console.log(error));
```

<hr></hr>

Want to read more interview posts, checkout my frontend interview series.

%[https://rajatexplains.com/series/fe-interview]

If you liked what you read 🧑‍🏫 and got to learn new things, do hit like 👍 and subscribe 🔖 to my newsletter to get instantly notified whenever I drop in new content.

And don't forget to follow 🚀 me on

Hashnode - @[Rajat Jain](@rajatexplains)

Twitter - [@rajat_codes](https://twitter.com/rajat_codes)

Instagram - [@javascript_to_the_rescue](https://instagram.com/javascript_to_the_rescue)

LinkedIn - [Rajat Jain](https://www.linkedin.com/in/rajatjain-21/)

