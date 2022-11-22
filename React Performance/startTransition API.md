One of the key parts of the `startTransition` API is that it gives the ability for React to start and pause rendering. This API exists to solve a core problem in previous versions of React:

**I _everything_ is urgent, then _nothing_ is.**

In reality, we're used to waiting for _some things_ when using the web. Network requests take time to load. Coffee shop Wi-Fi can be flaky, which exacerbates the problem.

But there are also certain things which we're just _not_ okay waiting for: When we type a character, we expect it to show up on the screen. When we hover or click, we expect feedback and our tolerance for even the slightest delay is almost non-existent.

The purpose of the `startTransition` API to help denote which APIs are urgent (e.g. update the input field that a user is typing into) and which can wait (e.g. doing some kind of expensive operation like sorting a big list)

It provides us with two basic mechanisms for doing this: [`useTransition`](useTransition.md) and [`useDeferredValue`](useDeferredValue.md).

Here is an example of the `startTransition` API:

```js
const onChange = (e) => {
  const value = e.target.value;
  setInputValue(value);
  startTransition(() => {
    setSearchValue(value);
  });
};
```

The cool part of the `useTransition` hook is that it also gives us an `isPending` boolean that we can use to show a loading state if we deem necessary.

```js
const [isPending, startTransition] = useTransition();
```

In React 18, concurrent rendering is able to:

- **Yield** periodically to the browser and give it a chance to fire one or more side effects.
- **Interrupt** the rendering process when it detects now urgent updates and start working on those urgent things before going back to non-urgent things.
- **Discard** outdate transition uodates and render the latest and greatest state.
