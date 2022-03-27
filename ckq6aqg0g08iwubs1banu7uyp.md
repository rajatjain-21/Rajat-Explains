## Implement Promise.all

This blog post is a part of the  [frontend interview series](https://rajatexplains.com/series/fe-interview). Do check out other posts.

Ignoring the cheesy subtitle of the blog 😛, let us get serious here for sometime.

The interviewer won't ask you this direct question, mostly. At least if I was the interviewer, I won't. I lead the candidate to a point where she/he has to implement `Promise.all` API.

Without wasting any more time, let us jump onto the question.
### Question
![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1624254043731/sfThojHfB.png)
<mark>Before going forward in the blog, first think of a solution in your head and code it out. Obviously looking at the blog title, you will be knowing what to do. But, be honest with yourself for a bit, and solve the question without using Promise.all.</mark>

Given an promise generator function, which takes in an integer and returns a promise which on resolution, will resolve with the passed in value after some random time limit. Now, we create an array of such promises. Now, you have been asked to write a function `printInOrder` to take in the array of promises and resolve all of them in a way that the logged output is in order from 1 to 10.

Before jumping into writing a solution, we have to first understand that if we just iterate over the promise and keep on resolving and logging the values, it won't print in order. If you didn't get it, go to the question again, and give it a moment. The random order is because of the random timeout value of resolution.
> 
The interviewer can quite easily drift into discussing [Microtask Queue and Event Loop](https://javascript.info/event-loop) 

Now, you should think of an approach in which the code will wait for the previous promise to be resolved and then go on to resolve the next one. Only then the result will come in order.

### `Promise.all` comes for the rescue
`all` is a [static](https://developer.mozilla.org/en-US/docs/Glossary/static_method) method inside `Promise` class. The `Promise.all()` method takes an iterable of promises as the input, and returns a single `Promise` that resolves to an array of the results of the input promises. An interesting point to note here is the resolution array contains the values in the same order it was passed to the `Promise.all` method.


%[https://codesandbox.io/s/promise-all-6i3i2?file=/src/index.js]
Check the console in the above [codesandbox](https://codesandbox.io/s/promise-all-6i3i2?file=/src/index.js) link. You can play around with it, by removing `Promise.all` and resolving the promises by just iterating and checkout what is the result that gets logged.

Till now, everything's good and all hunky dory.

**Now, comes the second part of the question.** Now, that you have figured out that it can be done using the `Promise.all` API, can you **implement it from scratch**. Assume that `Promise` class is missing the `all` method.

### Observations: What do we know about the method
- It is a static method in Promise class.
- It takes in an array of promises.
- It returns a promise which on resolution will return the array of resolved values from each promise in the same order in which they were passed.

### First Solution
```javascript
class MyPromise {
  static all(promises) {
  	let results = []; // to be resolved array of values
    let resolveCount = 0;
    // return a promise
    return new Promise((resolve, reject) => {
      promises.forEach((promise, index) => {
        promise.then((value) => {
          results[index] = value;
          resolveCount++;
          if(resolveCount === promises.length) {
            // to break the loop
            resolve(results);
          }
        }).catch(function (error) {
          reject(error);
        });
      });
    });
  }
}
```
Here, we have an array of `results`, which will be populated **index wise** whenever each promise is resolved. Here, `Closure` is playing a very important role. Although all the promises are being resolved asynchronously, the corresponding value of `index` and results array is accessible inside the individual promise's `then` method.

Well done! 👏, you have solved the problem and implemented the `Promise.all` API from scratch. You tested the code and everything is working fine with the question.

Well Well Well

Now, that you are implementing `Promise.all`, you need to consider the edge cases too.

### Edge cases
- `all([])` should resolve right away with []
- `all([1,2,3, Promise.resolve(4)])` should resolve right away with [1, 2, 3, 4]
- `all([1,2,3, Promise.reject('error')])` should reject

Let us refine our code and reach to a final solution.

### Final Solution
```javascript
let results = []; // to be resolved array of values
// return a promise
return new Promise((resolve, reject) => {
  // checking for [] array
  if (promises.length === 0) {
    resolve([]);
  } else {
    promises.forEach((promise, index) => {
      if (promise instanceof Promise) {
        promise.then((value) => {
          results[index] = value;
          if(results.length === promises.length) {
            // to break the loop
            resolve(results);
          }
        }).catch(function (error) {
          reject(error);
        });
      } else {
        // what if the array value is just a number
        results[index] = promise;
      }
      if(results.length === promises.length) {
        // to break the loop
        resolve(results);
      }
    });
  }
});
```

Well, there you go 🚀


### Criteria you will be judged on
- How well acquainted are you with Promise APIs
- Are you comfortable with Promises and playing around with them
- How good are you in finding edge cases and modify your code accordingly.

<hr></hr>

If you liked what you read 🧑‍🏫 and got to learn new things, do hit like 👍 and subscribe 🔖 to my newsletter to get instantly notified whenever I drop in new content.
And don't forget to follow 🚀 me on

Hashnode - @[Rajat Jain](@rajatexplains)

Twitter - [@rajat_codes](https://twitter.com/rajat_codes)

Instagram - [@javascript_to_the_rescue](https://instagram.com/javascript_to_the_rescue)

LinkedIn - [Rajat Jain](https://www.linkedin.com/in/rajatjain-21/)

