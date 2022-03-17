## Design an LRU cache

# What is an LRU Cache
Let’s first quickly go over what is **cache**. Computers have cache memory that temporarily stores the most frequently used data. It’s a great way to get the data that is used most often because the retrieval process is super fast. However, cache memory is limited in size and there needs to be a way to manage what data needs to be removed from the cache in order to store new data. That’s where LRU cache comes in.

**LRU** (**L**east **R**ecently **U**sed) is a cache replacement algorithm that removes the **least recently used** data in order to make room for new data. When I say "least recently used" data, I mean that of all the data we have in cache, this one has not been used or interacted with for the longest amount of time. 

Chrome uses LRU algorithm to evict (remove) data, when it has to. Watch [this video](https://youtu.be/NNuTV-gjlZQ?t=387) to get a better understanding around this (6:25 to 7:38)

# Question❓
Implement a class `LRUStorage`.

1. This is of course not to reflect the true implementation in Chrome.
1. `getData` and `setData` should both be treated as data being 'used'.
1. Considering time precision issue, your class needs to accept `getTimestamp` as second argument of constructor function. This method will be called to get the latest time.

```typescript
// type of a data instance stored cache
interface Data {
  key: string
  lastUsed: number
  size: number
  persistent: boolean
}

interface LRUStorage {
  // maximum data cache can store
  capacity: number

  // to use the data for key
  // return size of the data or undefined if not exist
  getData(key: string): Data | undefined
  
  // updating data for key
  // return boolean to indicate success or failure
  // If the total size exceeds capacity,
  // Least Recently Used non-persistent data other than itself should be evicted.
  setData(key: string, size: number): boolean

  // manually clear data for key
  clearData(key: string): void

  // change data for key to be persistent
  // it only handles existing data not the data added later
  // persistent data cannot be evicted unless manually clear it
  makePersistent(key: string): void 
}
```
# Motive of the question 🤔
With this question, the interviewer mainly wants to test you on:
1. How well do you understand class paradigm in JS
1. Your architecture design skills
1. How well do you think of the edge cases and efficiently figure out a solution

# Solution 👨‍💻
Let us create every function one by one. One step at a time.

### Step I
Let us first create the constructor for our `LRUStorage` class.
```typescript
class LRUStorage  {
  /**
   * @param {number} capacity
   * @param {() => number} getTimestamp
   */
  constructor(capacity, getTimestamp) {
    this.capacity = capacity
    this.getTimestamp = getTimestamp
    this.cache = {}
    this.currentCapacity = this.capacity
  }
}
```
We added a cache object, which will be our LRU cache, holding our data in form of key value pairs. We also need another variable to store the amount of memory currently used up, so that we can later evict our cache based on this.

### Step II
Let us now implement the `getData` method
```typescript
getData(key) {
  if (key in this.cache) {
    // don't forget to update the lastUsed value for this key
    this.cache[key].lastUsed = this.getTimestamp();
    return this.cache[key];
  }
}
```
### Step III
Now, let us implement the second method `setData`. Following are the caveats associated with the method:
1. update data for the key
1. return boolean to indicate success or failure
1. If the total size including the new data exceeds capacity, **Least Recently Used** non-persistent key data other than itself should be evicted.

Before we write the `setData` method, let us write the `evictData` method
```javascript
evictData() {
  const nonPersistentCacheKeys = Object.keys(this.cache).filter((item) => {
    return !this.cache[item].persistent;
  });
  const keyToRemove = cacheKeys.sort(
    (x, y) => this.cache[x].lastUsed - this.cache[y].lastUsed
  )[0];
  if (!keyToRemove) return false;
  this.currentCapacity += this.cache[keyToRemove].size;
  delete this.cache[keyToRemove];

  return true;
}
```

Now, let us write the `setData` method
```javascript
setData(key, size) {
  if (size > this.capacity) return false;

  if (key in this.cache && size <= this.cache[key].size) {
    this.currentCapacity =
      this.currentCapacity - this.cache[key].size + size;
    this.cache[key] = {
      lastUsed: this.getTimestamp(),
      size,
      persistent: false,
    };
  } else {
    while (this.currentCapacity < size) {
      const didEvict = this.evictData();
      if (!didEvict) return false; // not possible to remove any cache element
    }
    this.cache[key] = {
      lastUsed: this.getTimestamp(),
      size,
      persistent: false,
    };
    this.currentCapacity -= size;
  }
  return true;
}
```
### Step IV
Now, we have two methods left to implement, `clearCache` and `makePersistent`. These two are fairly straight forward
```javascript
clearData(key) {
  const spaceToRelease = this.cache[key].size;
  delete this.cache[key];
  this.currentCapacity += spaceToRelease;
}

makePersistent(key) {
  this.cache[key].persistent = true;
}
```
If you have any other solution or any issue in understanding this solution, do drop a comment 😊

<hr></hr>

Want to learn more through interview questions, checkout my frontend interview series.

%[https://rajatexplains.com/series/fe-interview]

If you liked what you read 🧑‍🏫 and got to learn new things, do hit like 👍 and subscribe 🔖 to my newsletter to get instantly notified whenever I drop in new content.

And don't forget to follow 🚀 me on

Hashnode - @[Rajat Jain](@rajatexplains)

Twitter - [@rajatexplains](https://twitter.com/rajatexplains)

Instagram - [@javascript_to_the_rescue](https://instagram.com/javascript_to_the_rescue)

LinkedIn - [Rajat Jain](https://www.linkedin.com/in/rajatjain-21/)
