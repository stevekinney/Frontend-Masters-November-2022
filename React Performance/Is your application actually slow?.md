Before you start taking every tip and trick you learned after half-reading a blog post, it's worth the time to sit down and figure out if your application is _actually_ slow.

1. Make sure that you're running a production build. All of that tooling that makes your life easier in development is slowing stuff down. So, what's slow in developer might not be slow in production. Save yourself time by not fixing problems that you don't have and make sure that you're observing the performance bottleneck in the production build.
2. Now, before you try all of these tricks, make sure you didn't put state higher than necessary in the application. In this course, we talk about this as the careful dance of [lifting state up or pushing that state down to the lowest common denominator](Pushing%20state%20down.md).
3. Finally, you can pull out the React DevTools Profiler to see if you have some unnecessary renders and then you can go ahead and pull out your toolbox of [`React.memo`](React.memo.md) and [`useMemo`](useMemo%20and%20useCallback.md) in order to optimize the performance of your application.

## Examples

- Show how to move the state down in Jetsetter.
- Give studens an exercise where they have to optimize the performance of an application themselves.

## An artificially slow component

```jsx
const sleep = (delay = 100) => {
  let now = performance.now();
  while (performance.now() - now < delay) {
    // Gum up the works
  }
}

const ExpensiveComponent({ children, delay }) {
  sleep(delay);
  return <>{children}</>;
}
```
