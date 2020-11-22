## Basics of Redux in 20 minutes


> Prerequisites = Fundamentals of React (mainly updation of states)

[Redux](https://redux.js.org/) is an open-source JavaScript library for managing application state, mainly used with React, though it’s not tied to the library and hence can be used with other stacks as well. Enough with the bookish introduction, let’s first understand in simple terms why do we need Redux.

## Why Redux

React has its own way of managing state. As we all know, that in React, our UI is the function of state, as state changes, the required components need to change accordingly. Now, for small applications, it can be done by passing on states as props to different child components, but as soon as the hierarchical tree gets bigger and deeper, it becomes a pain in the ass to manage states. Here redux comes to help.

> React in version 16.3.0 introduced the [**Context API**](https://reactjs.org/docs/context.html), which makes redux a bit redundant for this use case of accessing the state from different parts of your app, so consider using the Context API instead of Redux, unless you need a specific feature that Redux provides.

Redux is a way to manage an application state and moving to an external global store.

## When to use Redux

Redux is ideally used with apps of medium to high complexity, and you should only use it when you have trouble managing the state with the default state management of React. Having said that, if you want to use Redux with simple apps also, no one is stopping you to do so. It’s just that there is one principal that we follow and that is, never add a dependency unless you actually need it. Keep your app as less complex as possible.
Now let’s understand some more cool things about the library.

## Immutable State Tree

In Redux, the whole state of the application is represented by one JS object called **State** or **State Tree**. It’s called immutable because it is a read only; it can’t be changed directly. It can only be changed by dispatching an **Action**.

## Actions

An Action is **a JS object that describes a change in a minimal way** (with just the information needed)
```
{
   type: "CLICKED_NAVBAR"
}
//e.g.: with more data
{
   type: "SELECTED_DATA",
   userId: "123"
}
```

The only requirement of an action object is having a _type_ property, whose value is usually a string.

## Action types should be constants

In a simple app an action type can be defined as a string, as I did above. When the app grows, it’s best to use constants:
```
const ADD_ITEM = 'ADD_ITEM"
const action = { type: ADD_ITEM, title: 'My item' }
```


and to separate actions in their own file, import them
```
import { ADD_ITEM, REMOVE_ITEM } from './actions'
```


## Action creators

Action creators are functions that create actions:
```
function addItem(s) {
   return {
      type: ADD_ITEM,
      title: s
   }
}
```

You actually run action creators in combination with triggering the **dispatcher**:
```
dispatch(addItem('Books'))
```


or by defining an action dispatcher function:
```
const dispatchAddItem = i => dispatch(addItem(i))
dispatchAddItem('Books')
```

## Reducers

When an action is fired, something must happen, the state of the application must change (after all that is what the entire fight is for). This is what **reducers** do.

## What is a reducer?

A reducer is a pure function which calculates the next State Tree based on the previous State Tree based on the action dispatched.
```
function = (currentState, action) => newState
```


A pure function takes an input and returns an output without changing the input or anything else. Thus a reducer returns a completely new state tree object that substitutes the previous one. Mind this, it cannot mutate the state directly.

What a reducer should not do

A reducer should be a pure function, so it should

1.  never mutate its arguments
2.  never mutate the state, but instead create a new one.
3.  never generate side effects (no API calls changing anything)
4.  never call non-pure function, functions that change their output based on factors other than their input (e.g : Date.now() or Math.random() )

Again, there is no one forcing you to follow these, but it’s better to stick to these rules.

## Multiple Reducers

Since the state of a complex app could be really wide, there is not a single reducer, but many reducers for any kind of actions.

Now let’s understand the core implementation of redux in an application.

## Simulation of a reducer

**The state**
```
{
   list: [
    { title: "First Item"},
    { title: "Second Item"}
   ],
   title: "Book List"
}
```

**A list of actions**
```
{ type: "ADD_ITEM", title: "Third Item" },
{ type: "REMOVE_ITEM", index: 1 },
{ type: "CHANGE_LIST_TITLE", title: "Dedicated Book list" }
```

**A reducer for every part of the state**
```
//reducer for the title, that's why state is a string in the parameter
const title = (state = '', action) => {
 if (action.type === "CHANGE_LIST_TITLE") {
  return action.title
 } else {
  return state
 }
}
//reducer for list
const list = (state = [], action) => {
 switch(action.type) {
  case "ADD_ITEM":
   return state.concat([{ title: action.title }])
  case "REMOVE_ITEM":
   return state.map((item, index) => {
    action.index === index
    ? { title: item.title }
    : item
   })
  default:
   return state
 }
}
```

**A reducer for the whole state**
```
const listManager = (state = {}, action) => {
 return {
  title: title(state.title, action),
  list: list(state.list, action)
 }
}
```

## The Store

The Store is an object which

1.  **holds the state** of the app
2.  **exposes the state** via _getState()_
3.  allows us to **update the state** via _dispatch()_
4.  allows us to (un)register a **state change listener** using subscribe()

The Store is unique in an app and contains all the states in the app.
Here is how a store for listManager app is created:
```
import { createStore } from 'redux'
import listManager from './reducers'
let store = createStore(listManager)
```

You can also initialise the store from server-side data, just by passing a start state
```
let store = createStore(listManager, preExistingData)
```

**Getting the state**
```
store.getState()
```

**Update the state**
```
store.dispatch(addItem('Some Book'))
```

**Listen to state changes**
```
const unsubscribe = store.subscribe(() => {
 const newState = store.getState()
})
unsubscribe();
```

![Image result for redux](https://miro.medium.com/proxy/1*87dJ5EB3ydD7_AbhKb4UOQ.png)How the Store is a single source of truth for the app states.

To simply things, we can understand all this flow in this way:

## Data Flow

<noscript><img alt="Image for post" class="t u v cb aj" src="https://miro.medium.com/max/1176/1*NTKLDEcb018jQIjILxtp4Q.png" width="588" height="439" srcSet="https://miro.medium.com/max/552/1*NTKLDEcb018jQIjILxtp4Q.png 276w, https://miro.medium.com/max/1104/1*NTKLDEcb018jQIjILxtp4Q.png 552w, https://miro.medium.com/max/1176/1*NTKLDEcb018jQIjILxtp4Q.png 588w" sizes="588px"/></noscript>

### Data flow in Redux

Data flow in Redux is **uni-directional**.
You call dispatch() on Store, passing an Action.
The Store takes care of passing the Action to the Reducer, generating the nextState.
The Store then updates the state and alerts all the listeners.
Thus, there is only one global source of truth for all the state changes, and in this way we exempt ourselves from the headache of managing states in the component tree.

I hoped this article helped in clearing your concepts around Redux. This will not make you a master, for that you’ll have to use it in some application and feel the wonders. But one thing for sure, this will certainly help you to start and understand things better

Learn . Grow . Excel :)