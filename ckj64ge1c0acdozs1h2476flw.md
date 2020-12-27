## Make your JS objects iterable


We know when we want to iterate over an object, we can iterate over the keys by using `for..in` loop. It iterates over the list of enumerable properties on an object (including its `[[Prototype]]` chain).

![Image of iterating over the keys of an object using for..in](https://cdn.hashnode.com/res/hashnode/image/upload/v1609002215396/1c_Bd5lqN.png)

If we want to iterate over the values like we do in array using the `for..of` loop, we get this
![It throws an error that the object is not iterable](https://cdn.hashnode.com/res/hashnode/image/upload/v1609001816054/fFnmaaLu0.png)

> Arrays are also objects with keys as indices [12, 23, 45] => { 0: 12, 1: 23, 2: 45 }

![Joey Tribbiani saying Why God Why!!](https://media.giphy.com/media/Kg2tFStNdUsOmxv2GC/giphy.gif)

**We can use `for..of` with objects the way we do with arrays if the object defines its own custom iterator.**

Wait what!!! 🤔🧐 Well, how does it work for arrays then, aren't arrays typeof object too?
Well yeah 😎

Don't worry. Let us first understand how `for..of` loop works for arrays.

### How come arrays are iterable, despite of being objects?

```javascript
let myArray = [ 1, 2, 3 ];

for (let v of myArray) {
	console.log( v );
}
// 1
// 2
// 3
```
The for..of loop asks for an iterator object (from a default internal function known as `@@iterator` in spec-speak) of the thing to be iterated, and the loop then iterates over the successive return values from calling that iterator object's next() method, once for each loop iteration.
> Arrays have a built-in `@@iterator`, so for..of works easily on them. Well..well..well 🤨

Let us manually iterate over the array using that built-in `@@iterator` method
```javascript
let myArray = [ 1, 2, 3 ];
let it = myArray[Symbol.iterator]();

it.next(); // { value:1, done:false }
it.next(); // { value:2, done:false }
it.next(); // { value:3, done:false }
it.next(); // { done:true }
```
**Points to be noted:**

1. We get at the `@@iterator` internal property of an object using an ES6 Symbol: Symbol.iterator.
1. Despite the name's implications, `@@iterator` is not the iterator object itself, but a function that returns the iterator object.
1. The return value from an iterator's `next()` call is an object of the form 
`{ value: .. , done: .. }`, where value is the current iteration value, and done is a boolean that indicates if there's more to iterate.



While arrays do automatically iterate in `for..of` loops, regular objects do not have a built-in `@@iterator` method. Hmm.. 🤨 What do we do now?

Let us create our own custom `@@iterator` for an object that we want to iterate over.

### Making objects iterable by adding our own `@@iterator`
```javascript
let myObject = {
	a: 42,
	b: 34
};

Object.defineProperty( myObject, Symbol.iterator, {
	enumerable: false,
	writable: false,
	configurable: true,
	value: function() {
		let _this  = this;
		let idx = 0;
		let keysArr = Object.keys( _this );
		return {
			next: () => {
				return {
					value: _this[keysArr[idx++]],
					done: (idx > keysArr.length)
				};
			}
		};
	}
} );

// iterate `myObject` manually
let it = myObject[Symbol.iterator]();
it.next(); // { value:42, done:false }
it.next(); // { value:34, done:false }
it.next(); // { value:undefined, done:true }

```

Using `for..of` now works 🥰
```javascript
// iterate `myObject` with `for..of`
for (let v of myObject) {
	console.log( v );
}
// 42
// 34
```

**Let us understand the code here:**

- For adding a custom `@@iterator` to `myObject`, we use the static [defineProperty](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty) method on Object.
- We don't want this property to be `enumerable` or `writable`. So, we set their value to false.
- We put the actual method in the `value`. The method will return an object with `next` method on it. The `next` method will return the next enumerable values from the objects every time it is called.
- We make the use of [Javascript Closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) here. We pass the context of the object, the starting index of the object (0) and the object keys array as part of the closure to the `next` method.
- Each time the `for..of` loop calls `next()` on myObject's iterator object, the internal pointer will advance and return back the next value from the object's properties list.


In this way, we can make our regular objects iterable 😊🚀


If you have any doubt or suggestion, do post it in the comments section.

If you learnt something new and want to see more such interesting JS content, please do follow me on 

Hashnode - @[Rajat Jain](@rajatexplains)

Twitter - [@rajat_codes](https://twitter.com/rajat_codes)

Instagram - [@javascript_to_the_rescue](https://instagram.com/javascript_to_the_rescue)

LinkedIn - [Rajat Jain](https://www.linkedin.com/in/rajatjain-21/)



