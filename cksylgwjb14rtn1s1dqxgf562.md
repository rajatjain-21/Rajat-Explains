## Put most of your logic in reducers

The context of this blog is around  [Redux](https://redux.js.org)  and its applications. To get a primer on Redux, checkout my blog:

%[https://rajatexplains.com/basics-of-redux-in-20-minutes]

## Reducers
A **reducer** (also called a reducing function) is a function that accepts an accumulation and a value and returns a new accumulation. They are used to reduce a collection of values down to a single value.

In Redux, the accumulated value is the state object, and the values being accumulated are actions. Reducers, being *pure functions*, calculate a new state given the previous state and an action.
```javascript
const listReducer = (state=initialState, action) => {
  switch(action.type) {
     // different cases returning new state object
    ...
    default:
      return state;
  }
}
```

> Pure functions: 
1. Given the same input, always returns the same output.
1. Depends only on the input parameters.
1. Produces no side effects.

## Put as much logic inside reducers as possible

Wherever possible, **try to put as much of the logic for calculating a new state into the appropriate reducer, rather than in the code that prepares and dispatches the action **(like a click handler).

> The Redux core does not actually care whether a new state value is calculated in the reducer or in the action creation logic. So, there won't be any error thrown with any of the approaches. It is just about the best practices and making your life better.

But why? Why can't I deduce the new state in my action creator code and just pass the new state to the reducer as action payload.

Got my point?

Okay, let us try to understand it using an example

```javascript
// Click handler:
const onTodoClicked = id => {
  // does all the calculation for new todos here
  const newTodos = todos.map(todo => {
    if (todo.id === id) {
      return { ...todo, completed: !todo.completed }
    }
    return todo
  })
  
  dispatch({ type: 'todos/toggleTodo', payload: { todos: newTodos } })
}

// Reducer:
case "todos/toggleTodo":
    return action.payload.todos;
```
Fair enough, right?

The only problem that I can find by looking at it is that, logically, it is better to have keep our components clean and put the logic of reduction inside the reducing method. The component need not be concerned about the logic behind the new state calculation.

But, the problem with this approach is not this simple.

Let us look at the other approach, by putting the logic inside the reducer method

```javascript
// Click handler:
const onTodoClicked = (id) => {
    dispatch({type: "todos/toggleTodo", payload: {id}})
}

// Reducer:
case "todos/toggleTodo": {
    return state.map(todo => {
        if(todo.id === action.payload.id) {
          return {...todo, completed: !todo.completed }
        }
        return todo
    })
}
```
This second approach of putting most of the logic inside the reducer function is preferred because of the following reasons:

1. Pure functions are easily testable as you can have an input, call the function and assert the desired output. 

 Hence, reducers are easy to test, because they are pure functions - you just call `const result = reducer(testState, action)`, and assert that the result is what you expected. So, the more logic you can put in a reducer, the more logic you have that is easily testable.

2. This is a silly one, but is often prone to mistakes. We know that as reducers are pure functions, we should not mutate the state directly inside them, instead create and return a new state object. Redux state updates must always follow  [the rules of immutable updates](https://redux.js.org/usage/structuring-reducers/immutable-update-patterns). 

 Now, most Redux users realize that they have to follow the rules inside a reducer, but it's not obvious that you also have to do this if the new state is calculated outside the reducer (like in the case of first code snippet). This can easily lead to mistakes like **accidental mutations**, or even reading a value from the Redux store and passing it right back inside an action. Doing the new state calculation inside a reducer avoids those mistakes.

3. > Time-travel debugging works by letting you "undo" a dispatched action, then either do something different or "redo" the action, thanks to Redux Dev Tools 🙏

  **a). If the reducer is buggy**: Hot-reloading of reducers normally involves re-running the new reducer with the existing actions. If you have a correct action but a buggy reducer, you can edit the reducer to fix the bug, hot-reload it, and you should get the correct state right away.

  **b). If the action/action creator is buggy**: In this case, you have to re-run the steps that led to that action being dispatched.

 So, it's easier to debug if more logic is in the reducer.

4. Finally, putting logic in reducers means you know where to look for the update logic.

 Well, inside the respective reducer, duhh 🔥!!, instead of having it scattered in random other parts of the application code.

> 
<mark style="background: "yellow";">Note: There are valid cases where some or all of the new state should be calculated first (such as generating a unique ID), but that should be kept to a minimum.

<hr></hr>

If you liked what you read 🧑‍🏫 and got to learn new things, do hit like 👍 and subscribe 🔖 to my newsletter to get instantly notified whenever I drop in new content.
And don't forget to follow 🚀 me on

Hashnode - @[Rajat Jain](@rajatexplains)

Twitter - [@rajat_codes](https://twitter.com/rajat_codes)

Instagram - [@javascript_to_the_rescue](https://instagram.com/javascript_to_the_rescue)

LinkedIn - [Rajat Jain](https://www.linkedin.com/in/rajatjain-21/)
