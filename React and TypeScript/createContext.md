---
repository: https://github.com/stevekinney/colors
branch: context-api
endBranch: create-context
---

Now that we know a little bit more about generics, we can make an abstraction for solving for what to do if our context value isn't set yet.

If using `as` isn't to your liking, you can take advantage of generics and closures.

Make a new file called `src/create-context.tsx` and this shell:

```tsx
import React from 'react';

export const createContext = <T extends {}>() => {};
```

We say, that `T` at least needs to be some kind of object _or_ it can be `null`.

Next up, we're going to use React's regular `createContext` function. Just like we've done during our time together, we're tell TypeScript what it _should_ be (i.e. `T`), but it _can_ be `undefined` while we get everything ready.

```tsx
export const createContext = <T extends {}>() => {
  const Context = React.createContext<A | undefined>(undefined);
};
```

That's just a normal context, just like we're created before. It's no different than when we did this in `src/context.tsx`:

```tsx
export const ColorContext = createContext({ state: initialState } as {
  state: ColorState;
  dispatch: Dispatch<ColorActions>;
});
```

Okay, so now we have our hands on it—what's next? Remember how we made our own hooks early? We're going to do a similar thing right now.

```tsx
export const createContext = <T extends {}>() => {
  const Context = React.createContext<T | undefined>(undefined);

  const useContext = () => {
    const ctx = React.useContext(Context);

    if (ctx === undefined) {
      throw new Error('useContext must be inside a Provider with a value.');
    }

    return ctx;
  };

  return [useContext, Context.Provider] as const;
};
```

What's happening?

- We know that the value of the context can either be `T` ot `undefined`.
- So, pull the value out of `React.useContext`, and take a look at it.
- Blow up if `ctx`, this means that we won't return anything in the case that `ctx` is `undefined.
- Well, if `ctx` can only either be `undefined` or `T` and it's not `undefined`, then it's `T`. Boom. No more guessing games.

### Getting our new functions

The last line returns our new `useContext` and `Context.Provider` as a tuple. This will cause TypeScript to treat it like an array of two very specific things that will not be changing. This allows TypeScript to be sure about the type of the first and second elements with worrying that anyone (e.g. you) will push or pop things off the array.

We end up with the following signature:

```tsx
const createContext: <T extends {}>() => readonly [
  () => T,
  React.Provider<T | undefined>,
];
```

## Putting it to work

Okay, now we need to use our cool new toys in `src/context.tsx`:

```tsx
import { Dispatch, PropsWithChildren, useReducer } from 'react';
import { createContext } from './create-context';
import colorReducer, { initialState } from './lib/color-reducer';

const [useContext, Provider] = createContext<{
  state: ColorState;
  dispatch: Dispatch<ColorActions>;
}>();

export const useColorContext = useContext;

export const ColorProvider = ({ children }: PropsWithChildren) => {
  const [state, dispatch] = useReducer(colorReducer, initialState);

  return <Provider value={{ state, dispatch }}>{children}</Provider>;
};
```

### Breaking it down

Our new `createContext` does not have any issues with it starting out as `undefined`.

```tsx
const [useContext, Provider] = createContext<{
  state: ColorState;
  dispatch: Dispatch<ColorActions>;
}>();
```

If we hover `useContext`, we'll see that it does _exactly_ what we want.

```tsx
const useContext: () => {
  state: ColorState;
  dispatch: Dispatch<ColorActions>;
};
```

_Nice_. Let'd export our fun new hook.

```tsx
export const useColorContext = useContext;
```

Lastly, we swap in our new `Provider` for the one we had before.

```tsx
export const ColorProvider = ({ children }: PropsWithChildren) => {
  const [state, dispatch] = useReducer(colorReducer, initialState);

  return <Provider value={{ state, dispatch }}>{children}</Provider>;
};
```

### Using it in our custom hooks

Because we already made some neat abstractions, we only need to swap out `React.useContext` with `useColorContext` in two places in `src/hooks.ts`:

```tsx
export const useDispatch = () => {
  const { dispatch } = useColorContext();
  return useMemo(() => dispatch, [dispatch]);
};

export const useHexColor = () => {
  const { state } = useColorContext();
  return useMemo(() => state.hexColor, [state]);
};
```

Everything should now work as expected.

## Your Mission

I'll leave it as an exercise to the reader (or, viewer), but could you get this working in the packing list application?