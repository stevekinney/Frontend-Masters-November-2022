You might have noticed that in React 18 applications you see `ReactDOM.createRoot` be used as opposed to `ReactDOM.render`. This is intentional and has implications for your application.

`render` is deprecated and considered to be a legacy API. You can still use it, but you're going to get a warning.

`createRoot` opts you in to [React's concurrent features](Concurrent%20features%7CReact's%20concurrent%20features.md). This is what you _should_ be using from this point forward.

This, of course, begs the question: What is a root? Well, if you you must know, it's a pointer to the top-level data structure that React uses to track a tree to render.

There are some other justifications for the internal API. If you look at the code samples, you'll notice that you no longer need to pass in the contaner component to `createRoot`, which makes sense since it basically never changes. And you don't _need_ to store the root onto the DOM node. (That said, React currently still does this today.)

`createRoot` also removes the `hydrate` method—it's been moved to the new `hydrateRoot` API—and removes the render callback, which makes way more sense in a world with partial hydration.

As I mentioned earlier, `ReactDOM.render` is still included in React 18, but using it will trigger a deprecation warning and it's generally not advised. If you _need_ to use it, then you can, but you should plan on addressing whatever issues are resulting in you needing to use it.

There is at least [a desire](https://github.com/reactwg/react-18/discussions/5#discussioncomment-795681) from the React team that library authors who are targetting React 18 and later can just assume that you're using `createRoot` instead of `render`, so in addition to all of the other performance niceties—such as [Batching](Batching.md)— you run the risk of having incompatibility with the libraries that you use if you _don't_ opt in to using `createRoot`.