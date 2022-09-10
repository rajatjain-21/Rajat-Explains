## Make your async functions fast

# Promises power many of our data fetching workflows 🔋
To make Promises easier to work with, async functions introduce the `async` and `await` keywords which allow us to get the benefits of Promises — waiting for an async call to complete before continuing — without the mental overhead of chaining `.then` calls and nesting Promises.

> In this blog post, I am assuming that the readers are well aware of Promise and async-await and have worked with them. That is why I have focussed on the aspect of making async fast and not delved much into the basic code walkthrough of the applications of Promise and async-await

# Well, Promises have some problems 🥲
### 𝟷. Promises don’t return values for use outside themselves.

There’s no straightforward way to get the data out of a Promise; you have to work inside the `.then` once you’ve started using them.

The reasoning makes sense: Promises are **asynchronous**, but they shouldn't block synchronous code that doesn't depend on them, so the asynchronous code has to be isolated.

Okay! Fair enough..(well, not really)

### 𝟸. Nested Promises are hard to keep track of.
If our content has multiple async steps, the nesting in Promises can become challenging to keep track of.

Because next steps have to happen inside a `.then()`, each async action further nests our code, which adds cognitive overhead and can make code hard to refactor and maintain as time goes on.

# Async functions make Promises easier to use...

The `await` tells our code to wait for the Promise to resolve, then hands back the resolved value of the Promise as the return value. This removes a lot of boilerplate associated with Promises, and that’s a good thing 🚀.

# ...but async functions also introduce new challenges.
Unfortunately, the convenience of async functions comes with some traps that aren't immediately obvious.

Let’s take a look at two examples:

### 𝟷. Two unrelated async operations that load data, then do separate things.
With Promises, we might set this up like so:
```javascript
// -------------------------------------------------------------------
// these functions simulate requests that need to run async
// -------------------------------------------------------------------
function asyncThing1() {
  return new Promise((resolve) => {
    setTimeout(() => resolve('Thing 1 is done!'), 2000);
  });
}

function asyncThing2() {
  return new Promise((resolve) => {
    setTimeout(() => resolve('Thing 2 is done!'), 2000);
  });
}

// -------------------------------------------------------------------
// this is the code that actually loads data
// -------------------------------------------------------------------
function doThings() {
  asyncThing1().then((thing1) => {
    // do something with the first response
    console.log(thing1);
  });

  asyncThing2().then((thing2) => {
    // do something with the second response
    console.log(thing2);
  });
}

doThings();
```
If you run this in your console, you’ll see that Thing 1 and Thing 2 complete at nearly the same time.

If we refactor to use async functions, our code might look like this:
```javascript
// 🚫 don’t do this in your real code; it’s slow!
async function doThings() {
  const thing1 = await asyncThing1();
  console.log(thing1);

  const thing2 = await asyncThing2();
  console.log(thing2);
}

doThings();
```
This looks nice and clean, but if we run it in our console we’ll notice an issue: the code now takes **twice as long to run**! 😱

Promises run in parallel because they don’t care what happens outside of them — that‘s why we can only access their content inside a `.then`. This means that both `asyncThing1()` and `asyncThing2()` run at the same time in our first example.

In async functions, `await` blocks any code that follows, from executing until the Promise has resolves, which means that our refactored code doesn't even start `asyncThing2()` until `asyncThing1()` has completed — that’s not good.

There’s good news, though: we can fix this without giving up on the benefits of async functions! 🎉

`Promise.all` to the rescue 💖
```javascript
// ✅ do this — async code is run in parallel!
async function doThings() {
  const p1 = asyncThing1();
  const p2 = asyncThing2();

  const [thing1, thing2] = await Promise.all([p1, p2]);

  console.log(thing1);
  console.log(thing2);
}

doThings();
```
Because async functions are Promises under the hood, we can run both `asyncThing1()` and `asyncThing2()` in parallel by calling them without `await`. Then we can use `await` and `Promise.all`, which returns an array of results once all Promises have completed.

**This allows the Promises to run in parallel again, but still gives us a pleasant-to-use syntax that avoids chaining and lets us treat the values in our Promises as standard return values.**

### 𝟸. Async operations with dependencies
In some cases, we'll have async operations that depend on the results of previous async operations. For example, if we want to load a list of blog posts from one API endpoint, then load comment data for each blog post from another.

With Promises, that setup might look like this:
```javascript
// -------------------------------------------------------------------
// these functions simulate network requests to load data from an API
// -------------------------------------------------------------------
function getBlogPosts() {
  const posts = [
    { id: 1, title: 'Post One', body: 'A blog post!' },
    { id: 2, title: 'Post Two', body: 'Another blog post!' },
    { id: 3, title: 'Post Three', body: 'A third blog post!' },
  ];

  return new Promise((resolve) => {
    setTimeout(() => resolve(posts), 200);
  });
}

function getBlogComments(postId) {
  const comments = [
    { postId: 1, comment: 'Great post!' },
    { postId: 2, comment: 'I like it.' },
    { postId: 1, comment: 'You make interesting points.' },
    { postId: 3, comment: 'Needs more corgis.' },
    { postId: 2, comment: 'Nice work!' },
  ];

  // get comments for the given post
  const postComments = comments.filter((comment) => comment.postId === postId);

  return new Promise((resolve) => {
    setTimeout(() => resolve(postComments), 300);
  });
}

// -------------------------------------------------------------------
// this is the code that actually loads data
// -------------------------------------------------------------------
function loadContent() {
  getBlogPosts().then((posts) => {
    for (post of posts) {
      getBlogComments(post.id).then((comments) => {
        console.log({ ...post, comments });
      });
    }
  });
}

loadContent();
```
This code is perfectly fine. However, the nesting is pretty deep here, and it's a little challenging to track where things are happening — we've got callbacks in loops in callbacks.

To clean this up, our first instinct might be to refactor this code to use async functions:
```javascript
async function loadContent() {
  const posts = await getBlogPosts();

  for (post of posts) {
    // using await here means the Promise has to resolve before the loop continue
    const comments = await getBlogComments(post.id);

    console.log({ ...post, comments });
  }
}

loadContent();
```
So much cleaner! 😍

Unfortunately, each request for comments now has to wait for the one before it to complete, meaning our code is now significantly slower — and it'll only get worse as we add more posts!

To clean this up, we can use `.map` instead of a for loop and create an array of Promises, then use `Promise.all` to wait for them to complete.
```javascript
async function loadContent() {
  const posts = await getBlogPosts();

  // instead of awaiting this call, create an array of Promises
  const promises = posts.map((post) => {
    return getBlogComments(post.id).then((comments) => {
      return { ...post, comments };
    });
  });

  // use await on Promise.all so the Promises execute in parallel
  const postsWithComments = await Promise.all(promises);

  console.log(postsWithComments);
}

loadContent();
```
This code is still much more readable than the nested Promises and for loops, and it allows the requests to load comments to happen in parallel.

# Keep async functions fast 💨
This is just one optimization to keep in mind when writing asynchronous code. Tuck it into your toolkit and keep your async functions fast!

![minion.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1662608470787/z4FYCkIgw.gif align="left")

Have other ideas for speeding up async functions? Do hit me up on [Twitter](https://twitter.com/rajatexplains)

<hr></hr>

If you liked what you read 🧑‍🏫 and got to learn new things, do hit like 👍 and subscribe 🔖 to my newsletter to get instantly notified whenever I drop in new content.

And don't forget to follow 🚀 me on

Hashnode - @[Rajat Jain](@rajatexplains)

Twitter - [@rajatexplains](https://twitter.com/rajatexplains)

Instagram - [@javascript_to_the_rescue](https://instagram.com/javascript_to_the_rescue)

LinkedIn - [Rajat Jain](https://www.linkedin.com/in/rajatjain-21/)


