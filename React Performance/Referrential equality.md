Even when we've used `React.memo`, React will do a shallow equality check. Basically, this means that React will take each prop and just do a `===` equality check with the previous iteration of that particular prop. Newly created objects, arrays, and functions are _not_ referrentially equal.

```js
{ foo: 1, bar: 2 } !== { foo: 1, bar: 2 }
[1,2,3] !== [1,2,3]
() => {} !== () => {}
```

This means that they will count as changes when `React.memo`-wrapped components do their comparison.

This is one place where **class-based components** have an advantage over **function components**. Methods on classes are referrentially the same between renders, while new functions defined inside a component are referrentially different each time. We'll look at how to use [`useCallback`](useMemo%20and%20useCallback.md) in order to create functions are are referrentially the same between renders.

### A word on element references

If React returns the _exact same_ reference as it did previously when rendering a component, it will skip re-referencing that particular component. The most common case is when using `props.children`, but you can also wrap a component instance is `useMemo` and it will be cached until the dependencies are changed and the function in `useMemo` is re-run.