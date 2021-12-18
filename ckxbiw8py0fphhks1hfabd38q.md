## useState hook - behind the hood

### Introduction 🍾
Hooks are special functions which let you "**hook into**" different React features. State, being one of its features, is hooked into by using the `useState` React hook.

When you call `useState`, you are telling React that you want this component to remember something:

```javascript
const [counter, setCounter] = useState(0);
const [text, setText] = useState("default text");
```
Every time your component renders, `useState` gives you an array containing two values:

1. The **state variable** (counter) with the value you stored.
1. The **state setter function** (setCounter) which can update the state variable and trigger React to render the component again.

### `useState` behavior in practice 🧑‍💻
Here is how, it all happens in action:
```javascript
const [index, setIndex] = useState(0);
```
1. **Your component renders the first time**. Because you passed `0` to `useState` as the initial value for index, it will return `[0, setIndex]`. React remembers `0` is the latest state value.
1. **You update the state**. When a user clicks the button, it calls `setIndex(index + 1)`. index is `0`, so it’s `setIndex(1)`. This tells React to remember index is `1` now and triggers another render.
1. **Your component’s second render**. React still sees `useState(0)`, but because React remembers that you set index to `1`, it returns `[1, setIndex]` instead.
1. And so on!



> 
💡 What does rendering mean?
>
Rendering just means React is calling our component. Render step is very much different from our render just means React is calling our component and definitely different than **mounting of our component in DOM**. **I'll talk more about that in my next blog. Stay tuned!**

### Motivation behind the blog 🚀
Before learning anything, we should always understand the why behind it. Why should we have the knowledge of how `useState` or for that matter any hook is implemented internally.

Ever wondered, how does React know which state to return? In other words, you defined a state variable naming it `counter` and another one `text` inside your component (see the snippet at the top), both of which are being generated along with their setters via `useState`. But, React internally does not predict what you will be naming your state variable. There is no “identifier” that is passed to useState, so how does it know which state to return when `count` is accessed? Does it rely on some magic like parsing your functions? 

The answer is NO.

Let us unwind the mystery here in this blog, shall we?

### 🧠 Logic behind the magic 🪄
React hooks rely on a **stable call order on every render of the same component**. This works well in practice because you have to follow the rule (“only call Hooks at the top level”), and thus hooks will always be called in the same order. Additionally,  [a linter plugin](https://www.npmjs.com/package/eslint-plugin-react-hooks)  catches most mistakes.

Well, this means that the order in which you call `useState` and define state variables, React internally takes care of that order only, maintaining an array of state pair <value, setter>. It also maintains the current pair index, which is set to 0 before rendering. Each time you call `useState`, React gives you the next state pair and increments the index. You can read more about this mechanism in  [React Hooks: Not Magic, Just Arrays](https://medium.com/@ryardley/react-hooks-not-magic-just-arrays-cd4f1857236e) .

### Implementing `useState` 💪
In this section, I'll try to explain via code, how `useState` functions under the hood. This doesn’t use React but it gives you an idea of how the function works internally and help develop a significant mental model:

```javascript
let componentHooks = [];
let currentHookIndex = 0;

// How useState works inside React (simplified).
function useState(initialState) {
  let pair = componentHooks[currentHookIndex];
  if (pair) {
    // This is not the first render,
    // so the state pair already exists.
    // Return it and prepare for next Hook call.
    currentHookIndex++;
    return pair;
  }

  // This is the first time we're rendering,
  // so create a state pair and store it.
  pair = [initialState, setState];

  function setState(nextState) {
    // When the user requests a state change,
    // put the new value into the pair.
    pair[0] = nextState;
    updateDOM();
  }

  // Store the pair for future renders
  // and prepare for the next Hook call.
  componentHooks[currentHookIndex] = pair;
  currentHookIndex++;
  return pair;
}
```

The comments added in the snippet above are self explanatory. This is how the `useState` hook internally works. The `updateDOM` method is for re-instigating updates to the DOM, whenever **state setter** method is called. We don't need to worry about its implementation here.

<hr></hr>

Want to read more interview posts, checkout my frontend interview series.

%[https://rajatexplains.com/series/fe-interview]

If you liked what you read 🧑‍🏫 and got to learn new things, do hit like 👍 and subscribe 🔖 to my newsletter to get instantly notified whenever I drop in new content.

And don't forget to follow 🚀 me on

Hashnode - @[Rajat Jain](@rajatexplains)

Twitter - [@rajat_codes](https://twitter.com/rajat_codes)

Instagram - [@javascript_to_the_rescue](https://instagram.com/javascript_to_the_rescue)

LinkedIn - [Rajat Jain](https://www.linkedin.com/in/rajatjain-21/)
