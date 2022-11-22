Higher-order components are a common pattern in React for separating out the **stateful** bits of your application from the **presentational** pieces. This has a bunch of small advantages:

- Now that your presentation components are completely separated from any application state—and, more importantly, the API calls associated with fetching that state—you can easily test out all sorts of different configurations—either in your unit tests or something like Storybook—by simply passing in different props rather than needed to stub APIs or recreate complicated state.
- This same separation makes everything just a bit more resusable. The higher-order component that fetches the data and passes it into a presentational component can be used across multiple presentational components. Conversely, your presentational components can be used across multiple higher-components. The pattern basically lets you pull apart your components like LEGOs and snap them together in various different configurations.

Let's look at this component:

```tsx
import { MouseEventHandler, useCallback, useReducer } from 'react';
import { getPosition } from '../lib/get-position';

const initialState = { x: 0, y: 0 };

type UpdatePositionAction = {
  type: 'updatePosition';
  payload: Partial<typeof initialState>;
};

const reducer = (state = { x: 0, y: 0 }, action: UpdatePositionAction) => {
  if (action.type === 'updatePosition') {
    return { ...state, ...action.payload };
  }

  return state;
};

export const Canvas = () => {
  const [{ x, y }, dispatch] = useReducer(reducer, initialState);

  const updatePosition = useCallback<MouseEventHandler>(
    (event) =>
      dispatch({ type: 'updatePosition', payload: getPosition(event) }),
    [dispatch],
  );

  return (
    <div
      className="relative h-full w-full bg-primary-700"
      onMouseMove={updatePosition}
    >
      <section className="absolute right-0 bg-primary-200 p-4 text-right">
        <p>
          <span className="font-bold">X</span>: {x}
        </p>
        <p>
          <span className="font-bold">Y</span>: {y}
        </p>
      </section>
    </div>
  );
};
```

The big issue here is that the UI of the component and the ability to track the mouse movement are bound together—you don't get one without the other. We want to separate them.

Now, I know what you must be thinking: All of this flexibility is going to make working with TypeScript harder, right? While there are definitely some considerations to be made, I'm going to go ahead and argue that if we are bold enough to endeavor to push our understanding of TypeScript—and its relationship to React—just a little further, then adding TypeScript adds little-to-no additional ceremony beyond using the higher-order component pattern itself.

Let's start with the easy part, separating out the presentational piece.

```tsx
export const Canvas = ({ x, y }: { x: number; y: number }) => {
  return (
    <div className="relative h-full w-full bg-primary-700">
      <section className="absolute right-0 bg-primary-200 p-4 text-right">
        <p>
          <span className="font-bold">X</span>: {x}
        </p>
        <p>
          <span className="font-bold">Y</span>: {y}
        </p>
      </section>
    </div>
  );
};
```

We, that's a lot simpler, isn't it? I think so. That said, we still need to do something with all of that additional complexity. If we could just delete it and have everything work, we wouldn't need this pattern, would we?

What we want to end up with is three components:

- `<Canvas />` a dumb component that just responds to the the props it's given.
- `withMouseMove`, which is a higher-order (e.g. "headless" component) that just wraps another component—like the one above—and passes in props relative to the position of the mouse.
- `<CanvasWithMouse />` the previous two combined. This is basically what we started with, but we'll also have the added benefit of the two separate pieces in addition.

## Implementing the higher-order component pattern

Okay, let's look at the higher-order component in all of it's glory. I'm going to omit the reducer and other fun state management stuff for a hot minute and just focus on this gem right here.

```tsx
export const withMouseMove =
  <T extends {}>(Component: React.ComponentType<MousePosition>) =>
  (props: Omit<T, keyof MousePosition>) => {
    const [{ x, y }, setPosition] = useState(initialState);

    const updatePosition = useCallback<MouseEventHandler>(
      (event) => {
        const { x, y } = getPosition(event);
        setPosition({ x, y });
      },
      [setPosition],
    );

    return (
      <div className="w-full h-full" onMouseMove={updatePosition}>
        <Component {...(props as T)} x={x} y={y} />
      </div>
    );
  };
```

Okay, let's break it down:

```tsx
export const withMouseMove =
  <T extends {}>(Component: React.ComponentType<MousePosition>) =>
  (props: Omit<T, keyof MousePosition>) => {
    // …
  };
```

`withMouseMove` is a function that returns a function that returns a component. (Say that five times fast.)

Let's start with this line:

```tsx
type MousePosition = {
  x: number;
  y: number;
};

const Component: React.ComponentType<MousePosition>;
```

We're looking for some kind of component that takes `x` and `y` props that are numbers, since that's ultimately what we're going to be passing in. You can read a bit more about [`React.ComponentType` here](React.ComponentType.md) as a fun bonus.

Then we're going to return a new function (and, remember, functions are components these days) that takes whatever the component that we passed in took, but _without_ `x` and `y`. Then we'll do all of all our state management inside of this function's closer and return a component with `x` and `y` plugging in but ready to be used with whatever else this function feels like taking.

That's what's happening in this piece.

```tsx
export const withMouseMove =
  <T extends {}>(Component: React.ComponentType<MousePosition>) =>
  (props: Omit<T, keyof MousePosition>) => {
    // …

    return (
      <div className="w-full h-full" onMouseMove={updatePosition}>
        <Component {...(props as T)} x={x} y={y} />
      </div>
    );
  };
```

## Moving on

With that, let's see how we would do this with [render props](Render%20props.md).
