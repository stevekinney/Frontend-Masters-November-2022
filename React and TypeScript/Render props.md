Another common pattern in React is the use of [render props](https://reactjs.org/docs/render-props.html). This pattern is similar to—and sometimes an alternative to using—[higher-order components](Higher-order%20components.md). This pattern was _real popular_ durng the time period when the new Context API was around but the `useContext` hook was not widely available just yet. That said, it's still a powerful pattern and has the same advantage as higher-order components in that allows you to separate the statefulness of you components from their presentation.

Typically, you'll have a property on the component doing the data fetching called `render`, which takes a function that receives the data and passes it to to a component. It looks something like this:

```ts
<DataProvider render={(data) => <h1>Hello {data.target}</h1>} />
```

Generally speaking, everything we've learned here will come to our aide. In fact, this is going to be one of those rare times that I'm going to cut you loose on trying to solve it yourself before I give you the answer.

Let's take the [MouseTracker example from the React documentation] (https://reactjs.org/docs/render-props.html#use-render-props-for-cross-cutting-concerns) and see if we can get it set up with TypeScript using a render prop.

## Implmenenting the render props pattern

Render props do what they say on the tin. They are function props that are called. You pass in a function that is called and render whatever you want as a child in that spot.

```ts
type MouseMoveProps = {
  render: ({ x, y }: MousePosition) => JSX.Element;
};
```

Now, we use our `render` prop like we would `children` in React.

```tsx
export const MouseMove = ({ render }: MouseMoveProps) => {
  const [{ x, y }, setPosition] = useState(initialState);

  const updatePosition = useCallback<MouseEventHandler>(
    (event) => {
      const { x, y } = getPosition(event);
      setPosition({ x, y });
    },
    [setPosition],
  );
  /* ----------------------------------------- 👇 ---------------- */
  return <div onMouseMove={updatePosition}>{render({ x, y })}</div>;
};
```

The short version of the answer is that we _know_ that the function needs to return `JSX.Element` in order to be valid when it's called in the parent component. We can also use TypeScript to enforce that it accepts the correct the arguments.

**Nota bene**: One thing to keep in mind is that because TypeScript is a superset of JavaScript it follows that you don't have to acknowledge all of the arguments being passed to the function. So, you can pass in a render function that doesn't take any of the arguments being passed to it and TypeScript is totally okay with that. This is same mechanic that allows you to ignore the second and third arguments in `Array.prototype.map`, for example. That said, if you _do_ use any of the arguments, then TypeScript has your back and is going to make sure they're the _right_ ones.

And now, just for fun, we'll look at the same example but this time with [custom hooks](Custom%20hooks.md).
