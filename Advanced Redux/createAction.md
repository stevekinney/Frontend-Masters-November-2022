Let's imagine this reducer for a minute:

```ts
type CounterState = { count: number };
type CounterAction =
  | { type: 'INCREMENT' | 'DECREMENT'; payload: number }
  | { type: 'RESET' };

export const reducer = (state: CounterState, action: CounterAction) => {
  if (action.type === 'INCREMENT') {
    return { count: state.count + action.payload };
  }

  if (action.type === 'DECREMENT') {
    return { count: state.count - action.payload };
  }

  if (action.type === 'RESET') {
    return { count: 0 };
  }

  return state;
};
```

In the past, we might have created a little action with a bunch of moving parts. We totally could have just done it by hand every time:

```ts
const increment = (amount: number) => {
  return {
    type: INCREMENT,
    payload: amount,
  };
}

const incrementAction = increment(19);
dispatch(incrementAction) /* or */ dispatch(increment(38));
```

`createAction` allows us to do something _a lot_ cleaner.

```ts
// This is a function that will return an action.
const resetActionCreator = createAction('RESET');
resetActionCreator.type; // 'RESET'
```

If you look, it has the following type:

```ts
const resetActionCreator: ActionCreatorWithoutPayload<"RESET">
```

We can create an action just by calling it:

```ts
const resetAction = resetActionCreator();
```

And, we get the following action:

```ts
const resetAction: {
  payload: undefined;
  type: "RESET";
}
```

If we look at it with something that takes an argument, it's not too different. Let's look at creating one that increments:

```ts
const incrementActionCreator = createAction('INCREMENT', (amount: number) => ({
  payload: amount,
}));
console.log(incrementActionCreator.type); // 'INCREMENT'
```

This one also has an interesting type:

```ts
const incrementActionCreator: ActionCreatorWithPreparedPayload<[amount: number], number, "INCREMENT", never, never>
```

It knows that it's going to take a number as an argument and the payload is already correctly typed as well. This means, you get fully type-safe actions for free. You don't have to think about it—and like you don't even _need_ to use Redux, much less Redux Toolkit in order to use these objects. They're just regular ol' objects.

Calling the action creator gets me the action that I am expecting.

```ts
const incrementAction = incrementActionCreator(5);
```

And it has the following type:

```ts
const incrementAction: {
  payload: number;
  type: "INCREMENT";
}
```

Also interestingly, since the `.type` property is on the function itself. I can literally just use the action creator functions in my conditionals inside of a reducer. So, a simplified version might look something like this:

```ts
import { createAction } from '@reduxjs/toolkit';

type CounterState = { count: number };

const increment = createAction('INCREMENT', (amount: number) => ({ payload: amount }));
const decrement = createAction('DECREMENT', (amount: number) => ({ payload: amount }));
const reset = createAction('RESET');

type CounterAction = ReturnType<typeof increment | typeof decrement | typeof reset>

export const reducer = (state: CounterState, action: CounterAction) => {
  if (action.type === increment.type) {
    return { count: state.count + action.payload };
  }

  if (action.type === decrement.type) {
    return { count: state.count - action.payload };
  }

  if (action.type === reset.type) {
    return { count: 0 };
  }

  return state;
};
```

I want you to notice two important things:

1. I don't really have to store the `action.type` properties in constants to make sure I don't mispell them or somethng.
2. Since I'm _not_ using Redux Toolkit's `createReducer` just yet, I need to do like one little thing to add extra nice type safety to my plain ol' JavaScript reducer, but—other than that—everything is correctly typed without much work on my part.

Let's take it for a quick spin with [[createAction Exercise|an exercise]].