Before we talk about what an external store is and how we can sync to it, let's just briefly remind ourselves of what some *internal stores* are:

- `props`
- `context`
- `useState`
- `useReducer`

So, like we can reason that an external store is anything outside of React.

Here is the TL;DR from [the React documentation](https://beta.reactjs.org/apis/react/useSyncExternalStore):

> `useSyncExternalStore` is a hook recommended for reading and subscribing from external data sources in a way that’s compatible with concurrent rendering features like selective hydration and time slicing.

It has acceps three arguments:

1. `subscribe`
2. `getSnapshot`
3. `getServerSnapshot`

Calling it looks a bit like this:

```js
const state = useSyncExternalStore(store.subscribe, store.getSnapshot);
```

Hypothetically, if you look at like a Redux store, you might notice two of its methods are shockingly similar.

- `store.subscribe`
- `store.getState`

The latter of the two is gets a—umm—*snapshot* of the current state of the Redux store. And obviously, it doesn't have to be a Redux store. It could be any library, but if you're familiar with Redux, that's a pretty cool mental model. (And React-Redux *does* use `useSyncExternalStore` under the hood.) But, like you could use DOM state as well if you wanted to.

One of the main goes of the `useSyncExternalStore` hooks to address the issue of [Tearing](Tearing.md) when using external data stores.

- [ ] [Data Changes During Rendering](https://github.com/reactwg/react-18/discussions/35#discussioncomment-823583)
