I really like the one-liner at the top of [the Suspense section in the beta version of the React Docs](https://beta.reactjs.org/apis/react/Suspense).

> `Suspense` is a React component that displays a fallback until its children have finished loading.

There are a few use cases for Suspense:

- Displaying a fallback while something is loading.
- Revealing nested content as it loads.
- Lazy-loading components with Suspense.

**Nota bene**: Right now, we're not going to go too deep into the weeds because Suspense doesn't work everywhere just yet, but I *do* think it's worth our time to go over the details.

Lazy-loading totally works right now.

## Further Reading

- [ ] [Changes to Suspense in React 18](https://github.com/reactwg/react-18/discussions/47#discussioncomment-847004)
- [ ] [Releasing Suspense](https://github.com/facebook/react/issues/13206)
- [ ] [Built-in Suspense Cache](https://github.com/reactwg/react-18/discussions/25)
- [ ] [Code-Splitting](https://reactjs.org/docs/code-splitting.html)
- [ ] [Lazy Loading React Components (with react.lazy and suspense)](https://blog.bitsrc.io/lazy-loading-react-components-with-react-lazy-and-suspense-f05c4cfde10c)
- [ ] [New Suspense SSR Architecture in React 18](https://github.com/reactwg/react-18/discussions/37)
- [ ] [Changes to Suspense effects semantics](https://github.com/reactwg/react-18/discussions/31)
- [ ] [Suspense (reactjs.org)](https://beta.reactjs.org/apis/react/Suspense)
