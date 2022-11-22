We know that updating the state triggers a render. But, React will trying to be somewhat smart about this and at least let you finish before it begins the process. This means that if you update the state multiple times, React will not stop and re-render each time. It's going to batch up all your changes and try to figure everything out in one go.

## Automatic Batching

> Batching is when React groups multiple state updates into a single re-render for better performance. Without automatic batching, we only batched updates inside React event handlers. Updates inside of promises, `setTimeout`, native event handlers, or any other event were not batched in React by default. With automatic batching, these updates will be batched automatically:

```jsx
// Before: only React events were batched.
setTimeout(() => {
  setCount((c) => c + 1);
  setFlag((f) => !f);
  // React will render twice, once for each state update (no batching)
}, 1000);

// After: updates inside of timeouts, promises,
// native event handlers or any other event are batched.
setTimeout(() => {
  setCount((c) => c + 1);
  setFlag((f) => !f);
  // React will only re-render once at the end (that's batching!)
}, 1000);
```

We know that updating the state triggers a render. But, React will trying to be somewhat smart about this and at least let you finish before it begins the process. This means that if you update the state multiple times, React will not stop and re-render each time. It's going to batch up all your changes and try to figure everything out in one go.

Batching is when React groups multiple state updates into a single re-render. [As we've discussed](Steve's%20Golden%20Rule%20of%20Performance.md), doing less things is usually better for performance. So, it makes sense that batching stuff into a single re-render is better. Batching updates into a single re-render also has the additional advantage of helping you avoid situations where your application renders in some weird, in-between state where the first update has rendered and the second update hasn't yet.

In the **passive effects phase**, state updates inside of `useEffect` hooks are batched up and handled at the end.

In previous version of React, it only batched updates that occured in event handlers. Other things that occured outside of React event handlers (e.g. setTimeout, promise resolution, after pausing for an `await` or in regular ol' JavaScript event handlers) were _not_ batched. But, React 18 does automatic batching for all of the updates that happen in a single event loop tick. That means you don't have to worry about this anymore.

In React 18—and as long as you're using `createRoot`, it _will_ batch updates in the situation described above. But, the important thing to note is that if you're still using the `render`, then you're not opting in to this new feature. So, just bumping the version in your `package.json` is not enough.

Prior to React 18, there were ways to manually batch updates via a—not at all scary—mechanism called `unstable_batchedUpdates()`. Since this now the standard behavior in React 18, we now have [a `flushSync()` API](https://beta.reactjs.org/apis/react-dom/flushSync) that will immediately trigger a re-render and opt you out of any automatic batching.

I should note that you only get these features if you [opt in using `ReactDOM.createRoot`](https://github.com/reactwg/react-18/discussions/5) over the legacy `React.render`. Even if you're using React 18+, using the logecy `render` will cause you to fallback to the old behavior. You can read more about [`render` versus `createRoot` here](render%20versus%20createRoot.md).