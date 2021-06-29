## Shuffle an array in place

### ❓Question
Write the function `shuffle(array)` that shuffles (randomly reorders) elements of the array.

Multiple runs of shuffle may lead to different orders of elements. For instance:
```javascript
let arr = [1, 2, 3];

shuffle(arr);
// arr = [3, 2, 1]

shuffle(arr);
// arr = [2, 1, 3]

shuffle(arr);
// arr = [3, 1, 2]
```
All element orders should have an equal probability. For instance, [1,2,3] can be reordered as [1,2,3] or [1,3,2] or [3,1,2] etc, with equal probability of each case.

<mark>Before jumping on the later sections, do give it a try yourselves. These blogs will only help if you grow while you learn.</mark>

### 🧠 Churning
**What things do we know?**
- We need to reorder the array **in place**
- All the orderings should be equally probable
- To check for the equal probability, we need to test our solution in a large test space.

### 💡 Solution #1
We know that arrays in JS have a `sort` method. The method allows you to sort elements of an array **in place**. It also takes in an optional argument, which is a **comparator** function. It is internally used to compare between two elements.
So, randomising the sort function can be one solution
```javascript
function shuffle(array) {
  array.sort(() => Math.random() - 0.5);
}

let arr = [1, 2, 3];
shuffle(arr);
alert(arr);
```
That somewhat works, because `Math.random() - 0.5` is a random number that may be positive or negative, so the sorting function reorders elements randomly.

But because the sorting function is not meant to be used this way, not all permutations have the same probability.

For instance, consider the code below. It runs shuffle 1000000 times and counts appearances of all possible results:

%[https://codepen.io/rajat_jain21/pen/rNmNqOd]

*As we can see, some of the orderings appear significantly more times than others!*

The result of the code may vary between JavaScript engines, but we can already see that the approach is unreliable.

### 🥸 Why solution #1 fails?
Generally speaking, `sort` is a **“black box”**: we throw an array and a comparison function into it and expect the array to be sorted. But due to the utter randomness of the comparison the black box goes mad, and how exactly it goes mad depends on the concrete implementation that differs between engines.

### 💡 Solution #2
There is one simpler and easily understandable way to solve the shuffling problem. This algorithm is called the  [Fisher-Yates](https://en.wikipedia.org/wiki/Fisher%E2%80%93Yates_shuffle)  shuffle. **The idea is to walk the array in the reverse order and swap each element with a random one before it**

%[https://www.youtube.com/watch?v=tLxBwSL3lPQ]

```javascript
function shuffle(array) {
  for (let i = array.length - 1; i > 0; i--) {
    let j = Math.floor(Math.random() * (i + 1));
    console.log(j) // random index from 0 to i
   
    // swap elements array[i] and array[j]
    let temp = array[i];
    array[i] = array[j];
    array[j] = temp;
  }
}
```

Now, let us test this algorithm on the same large test space:

%[https://codepen.io/rajat_jain21/pen/xxdxyMv]

Looks good now 🚀: all permutations appear with the same probability 🔥.

> 
Also, performance-wise the **Fisher-Yates algorithm** is much better, there’s no “sorting” overhead.


If you liked what you read 🧑‍🏫 and got to learn new things, do hit like 👍 and subscribe 🔖 to my newsletter to get instantly notified whenever I drop in new content.
And don't forget to follow 🚀 me on

Hashnode - @[Rajat Jain](@rajatexplains)

Twitter - [@rajat_codes](https://twitter.com/rajat_codes)

Instagram - [@javascript_to_the_rescue](https://instagram.com/javascript_to_the_rescue)

LinkedIn - [Rajat Jain](https://www.linkedin.com/in/rajatjain-21/)