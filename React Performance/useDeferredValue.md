It's not wrong to think of a deferring a value as something similar to throttling or debouncing it.

> If the current render is the result of an urgent update, like a user input, React will return the previous value. Then, it will switch to the new value after the current render has completed.

`useDeferredValue` is similar to `startTransition` from the [Transitions API](Transitions.md).

> - `startTransition` is used when triggering an update (i.e. setState) in an event handler.
> - `useDeferredValue` is used when receiving new data from a parent component (or an earlier hook in the same component).

## Further Reading

- [ ] [Feature discussion in the React Working Group](https://github.com/reactwg/react-18/discussions/129)
- [ ] [API Docs](https://reactjs.org/docs/hooks-reference.html#usedeferredvalue)
