## 4 must know cool features in Javascript

We as frontend developers try to optimise the way our webpages work. Javascript, with its cool new features help us optimise the way we write code. In a cleaner, better and less clumsier way.

Following are the four cool features in JS, which every Javascript developer should know and they can come handy in scenarios where otherwise you have to write more code.

Let us begin 🚀
## 1️⃣ Nullish Coalescing 🧼 👩‍❤️‍👨
First, let me explain the code snippet that I am going to use for demonstrating this feature.
```javascript
const calculateTaxes = (amount, tax, message) => {
  const multiplier = (1 + tax);
  const finalAmount = amount * multiplier;
  console.log(`Total amount is ${finalAmount}: ${message}`);
}
```
I have a function called `calculateTaxes`, which is taking in an amount, tax to be charged and a message and logging the final statement with final amount in console.

What if the value for tax or message is undefined or any falsy value?

Normally, we solve this using the `||` operator, where we pass in a default value.

```javascript
const calculateTaxes = (amount, tax, message) => {
  tax = tax || 0.05;
  message = message || "Default message";
  const multiplier = (1 + tax);
  const finalAmount = amount * multiplier;
  console.log(`Total amount is ${finalAmount}: ${message}`);
}

calculateTaxes(100, 0.07, "Hi there") // Total amount is 107: Hi there
calculateTaxes(100, 0, undefined) // Total amount is 105: Default message
calculateTaxes(100, undefined, "") // Total amount is 105: Default message
```
Notice, in the second log, as my taxes are 0, ideally **I should have got 100** as the `totalAmount`, but as 0 is a **falsy** value, alongwith `||`, it falls back to the default value. Similarly, in the third log, the message I am passing should be an empty string, but again being a **falsy** value, it falls back to the default message.

 [**Nullish Coalescing**](https://javascript.info/nullish-coalescing-operator)  to the rescue!
```
```javascript
const calculateTaxes = (amount, tax, message) => {
  tax = tax ?? 0.05;
  message = message ?? "Default message";
  const multiplier = (1 + tax);
  const finalAmount = amount * multiplier;
  console.log(`Total amount is ${finalAmount}: ${message}`);
}

calculateTaxes(100, 0.07, "Hi there") // Total amount is 107: Hi there
calculateTaxes(100, 0, undefined) // Total amount is 100: Default message
calculateTaxes(100, undefined, "") // Total amount is 105: 
```

## 2️⃣ Styling Console Log 💇🏻‍♂️ 🪵
Sometimes, during debugging, we add logs to the console and try to figure out what went wrong. Now, if you want your logs to be styled differently, you can do this simply by putting `%c` inside your console.log statement wherever you want to add a different style. You can add multiple styles to different components by adding multiple `%c` and adding styles as comma separated strings. It goes like this
```javascript
const calculateTaxes = (amount, tax, message) => {
  tax = tax ?? 0.05;
  message = message ?? "Default message";
  const multiplier = 1 + tax;
  const finalAmount = amount * multiplier;
  console.log(
    `%cTotal amount is ${finalAmount}: ${message}`,
    "font-weight: bold; color: red;"
  );
  console.log(
    `%cTotal amount is ${finalAmount}: %c${message}`,
    "font-weight: bold; color: red;",
    "color: yellow;"
  );
};

calculateTaxes(100, 0.08, "Hi there");
```
Such a cool thing, right 😀. Watch this in action here.

%[https://codesandbox.io/embed/friendly-elbakyan-t24p9?autoresize=1&expanddevtools=1&fontsize=14&hidenavigation=1&module=%2Fsrc%2Findex.js&theme=dark]

## 3️⃣ Optional Chaining ❓⛓
Optional Chaining is kind of like Nullish Coalescing, only way more powerful 🚀. Have a look at the code below.
```javascript
class Person {
  constructor(name, address, hobbies) {
    this.name = name;
    this.address = address;
    this.hobbies = hobbies;
  }

  print() {
    console.log(this)
  }
}

function printPersonStreet(person) {
  console.log(person.address.street);
}

const rajat = new Person(
  "Rajat",
  { street: "Harlur", city: "Bengaluru" },
  ["singing", "writing"]
)
rajat.print(); // logs the rajat object
printPersonStreet(rajat); // logs the value "Harlur"
```
Everything is good with this code. But what if, the address passed is **undefined**. In this case,`printPersonStreet` function will throw a `TypeError`, saying `can't find street of undefined`.

![image.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1630397173849/S3RBSmd40.png)

We can solve this by using the `&&` operator, so our `printPersonStreet` function will look something like this
```javascript
function printPersonStreet(person) {
  console.log(person && person.address && person.address.street);
}
```
Here, we are asking if there is a person, and if there is a person.address then log person.address.street. We can also do this by checking the condition inside an `if` first and then calling console.log. But, both the ways suck and this kind of a situation is very common in JS.

However, we have now got a cleaner and cooler feature to handle such scenarios.

 [**Optional Chaining**](https://javascript.info/optional-chaining)  to the rescue!

```javascript
function printPersonStreet(person) {
  console.log(person?.address?.street);
}
```
**Works the exact same way, only way cleaner!**✨ If the person does not exist, it will short-circuit from that point and return undefined. If `person` exists and `person.address` is not present, it will again return undefined and so on.

You can use optional chaining with functions too. Suppose you try to call a new function `printName` on class Person.
`rajat.printName();`. This will throw error, because class Person does not have a function called `printName`. So, you can do something like this:

```javascript
rajat.printName?.()
```

which means that if printName as a function exists, call it, otherwise do nothing. Looks a bit weird at first, but is so cool 😎 , right?

You can use optional chaining with arrays too.

Suppose, if you have the hobbies array, you want to print the first hobby.
```javascript
console.log(rajat.hobbies?.[0])
```

## 4️⃣ Object Shorthand 🤏🏻🪚
When you are constructing an object and **you have the value variables same as the key**, you can use the shorthand way there.
```javascript
const name = "Rajat"
const key = "0123"
// instead of writing like this
/* const person = {
  name: name,
  key: key
} */
// Shorthand way of creating object literals
const person = {
  name,
  key
}
```
It is just really simple and really small change, but it just drastically improves the speed with which you write your code 😀.

<mark>**Note**: It only works when the variable has the same name as the key in object.

<hr></hr>

If you liked what you read 🧑‍🏫 and got to learn new things, do hit like 👍 and subscribe 🔖 to my newsletter to get instantly notified whenever I drop in new content.
And don't forget to follow 🚀 me on

Hashnode - @[Rajat Jain](@rajatexplains)

Twitter - [@rajat_codes](https://twitter.com/rajat_codes)

Instagram - [@javascript_to_the_rescue](https://instagram.com/javascript_to_the_rescue)

LinkedIn - [Rajat Jain](https://www.linkedin.com/in/rajatjain-21/)






