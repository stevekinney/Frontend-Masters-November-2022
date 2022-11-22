---
repository: https://github.com/stevekinney/colors
branch: typing-actions-solution
endBranch: context-api
---

At first glance, React's Context API behaves just like we might expect.

```tsx
import { createContext } from 'react';
import { initialState } from './lib/color-reducer';

const ColorContext = createContext(initialState);
```

If you hover over `ColorContext`, you'll see:

```tsx
const ColorContext: React.Context<ColorState>;
```

And if you _just_ needed that you'd be fine.

![](_attachments/Pasted%20image%2020221111145036.png)

The problem is that a lot of the time we use the Context APi to help with state management. And part of state management is—you know—updating the state.

And to update the state, you tend to use hooks. And you definitely don't have those hooks outside of components.

The is futher exacerbated by the fact that you can only export `ColorContext` from the top-level of the module (e.g. _not_ inside a component where you can use hooks).

And so, it's a bit tempting to say that we want to do something like this:

```tsx
const ColorContext = createContext<{
  state: ColorState;
  dispatch: Dispatch<ColorActions>;
}>({ state: initialState });
```

The problem of course is that you know you're going to get yelled at for not having `dispatch` just yet.

![](_attachments/Pasted%20image%2020221111145603.png)

One option is to make `dispatch` optional, but then you're going to have to confirm it's existence _every_ time you use it—and that _not_ ideal.

```tsx
const ColorContext = createContext<{
  state: ColorState;
  dispatch?: Dispatch<ColorActions>;
}>({ state: initialState });
```

We're going to arrive at a slightly better solution in a bit, but we need to learn a few more things. But, one option is that since we _know_ that we're immediately going to write up `dispatch` (or, `useState`), we could tell TypeScript, "Hey, trust me, I know what I'm doing."

## Ideas that I am not willing to entertain

- **Using `any`**: This will opt us out of the whole reason we're using TypeScript in the first place.

## An escape hatch

This is an escape hatch that should fill you with an icky feeling, but it's mostly okay in this particular case. We can assert that an object meets a given type—even if TypeScript doesn't think that it does.

We did this already in `src/index.tsx`

```tsx
const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement,
);
```

See that `as HTMLElement`. Yea, like there is a chance that it's not on the page. `document.getElementById()` handles that telling you that it _might_ return `undefined`.

But, we _know_ it's on the page, so we told TypeScript that it's cool. We can do the same thing here too.

```ts
const ColorContext = createContext({ state: initialState } as {
  state: ColorState;
  dispatch: Dispatch<ColorActions>;
});
```

If this bother's you, we'll look at [a different—and arguably, better—way](createContext.md) to deal with this after we explore [generics](Generics.md) in a bit.

Now, we can go the rest of the way with the normal ceremony that we're used to.

```ts
import { createContext, Dispatch, PropsWithChildren, useReducer } from 'react';
import colorReducer, { initialState } from './lib/color-reducer';

export const ColorContext = createContext({ state: initialState } as {
  state: ColorState;
  dispatch: Dispatch<ColorActions>;
});

export const ColorProvider = ({ children }: PropsWithChildren) => {
  const [state, dispatch] = useReducer(colorReducer, initialState);

  return (
    <ColorContext.Provider value={{ state, dispatch }}>
      {children}
    </ColorContext.Provider>
  );
};
```

Don't forget to wrap your application in

The important thing to note is that despite our sneaky move, our type system holds up. We don't have to verify that `dispatch` exists constantly and when we go to use `state` and `dispatch`, everything will have the correct types.

## Using the context in your components

And again, we get a lot of the types for free. Let's take it for a spin in `src/components/shared/color-change-swatch.tsx`.

```tsx
import clsx from 'clsx';
import { MouseEventHandler, useContext } from 'react';
import { ColorContext } from '../../context';
import Button from './button';

type ColorChangeSwatchProps = {
  hexColor: string;
  className?: string;
  onClick?: MouseEventHandler<HTMLButtonElement>;
};

const ColorChangeSwatch = ({
  hexColor,
  className,
  onClick,
}: ColorChangeSwatchProps) => {
  const { dispatch } = useContext(ColorContext);

  return (
    <Button
      className={clsx(
        'border-2 border-slate-900 transition-shadow duration-200 ease-in hover:shadow-xl',
        className,
      )}
      style={{ backgroundColor: hexColor }}
      onClick={() =>
        dispatch({ type: 'update-hex-color', payload: { hexColor } })
      }
    >
      {hexColor}
    </Button>
  );
};

export default ColorChangeSwatch;
```

We can and should unwind the passing through of the `onClick` prop, but it's not bothering anyone for now and I'll probably just do that behind the seens as not to waste your precious time.

## Wiring up our new state

Now, it both works and it doesn't work at the same time. It totally works, but `src/components/application.tsx` is using a _different_ reducer right now.

This is an easy fix:

```tsx
const Application = () => {
  const { state, dispatch } = useContext(ColorContext);
  const hexColor = state.hexColor;

  return (
    <div className="grid max-w-3xl grid-cols-1 gap-8 p-8 pb-40 mx-auto dark:bg-slate-900 dark:text-white sm:grid-cols-2">
      <ColorPicker
        hexColor={hexColor}
        onChange={(e) =>
          dispatch({
            type: 'update-hex-color',
            payload: { hexColor: e.target.value },
          })
        }
      />
      <AdjustColors hexColor={hexColor} dispatch={dispatch} />
      <RelatedColors hexColor={hexColor} />
      <SavedColors hexColor={hexColor} dispatch={dispatch} />
    </div>
  );
};
```
