Consider the reducer we wrote in the little aside on [`createAction`](createAction.md).

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

That's just using a bespoke, artisinal reducer. And, it totally works. We had to do as little bit of tomfoolery, but we could use this with Redux and we could even use it with regular ol' `useReducer` in React.

But, `createAction` has a sibling: `createReducer`:

```ts
export const counterReducer = createReducer({ count: 0 }, (builder) => {
  // Do stuff!
})
```

That first argument is the initial value and the second one is an alternative syntax that allows us to easily compose reducer logic without a bunch of conditionals and all of the perils that come with that.

Let's look at what our previous reducer would look like if we refactored it.

```ts
export const counterReducer = createReducer({ count: 0 }, (builder) => {
  builder.addCase(increment, (state, action) => {
    state.count += action.payload;
  });

  builder.addCase(decrement, (state, action) => {
    state.count -= action.payload;
  });

  builder.addCase(reset, (state, action) => {
    state.count = 0;
  });

  builder.addDefaultCase((state, action) => {
    console.log('Whoops. It looks like you sent in bogus action somehow.');
  });
});
```



Some tasting notes:

1. I am just using my action creators and popping them in as the first argument to `builder.addCase` and it's all correctly wired up. If an action comes in that was created by one of those action creators, everything s gravy.
2. That's TypeScript. Everything is correctly typed.
3. It looks like I'm mutating state, but I'm really not. I'm using [Immer](Immer.md).

The fourth is that it's actually Redux agnostic. You could also just use this with a regular `useReducer` and everything is gravy. It's just a JavaScript function.

```ts
const three = counterReducer({ count: 0 }, increment(3));
```

This also makes it wildly easy to unit test the logic of your reducer. Just pass some stuff in and watch it go.

Cool, let's take it for a spin with [[createReducer Exercise|an exercise]].
