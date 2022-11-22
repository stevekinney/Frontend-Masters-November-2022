Let's say that you like the idea of using a reducer, but you _hate_ passing `dispatch` around. You can keep an API very similar to what we started with, but also still use this approach.

Let's look at too different options:

- We'll make a helper and memoize the results.
- We'll make a hook that takes care of the memoization for us.

## `bindActions`

Passing `dispatch` around and passing action creators to it can get tedious. Instead, we can create a helper function that just takes care of that chore for us.

A helper might look something like this:

```ts
import { useMemo } from 'react';
import { add, update, remove, markAllAsUnpacked } from './reducer';

export const bindActions = (dispatch) => {
  console.log('bindActions');
  const actions = {
    add: (...args) => dispatch(add(...args)),
    update: (...args) => dispatch(update(...args)),
    remove: (...args) => dispatch(remove(...args)),
    markAllAsUnpacked: (...args) => dispatch(markAllAsUnpacked(...args)),
  };

  return actions;
};
```

And now, we just replace all of our function in `Application` with this:

```ts
const { add, remove, update, markAllAsUnpacked } = useMemo(
  () => bindActions(dispatch),
  [dispatch],
);
```

Since `dispatch` doesn't change you'll only see our function log to the console once after we memozie it.

## `useItemActions`

React likes to make sure you're only using hooks where you're supposed to. You can only use hooks in one of two places.

- React components
- Custom hooks that you plan on using in React components.

In that second case, React asks that you follow the convention of prefixing your hooks with `use-`.

```ts
export const useItemActions = (dispatch) => {
  const actions = useMemo(() => {
    console.log('useItemActions');
    return {
      add: (...args) => dispatch(add(...args)),
      update: (...args) => dispatch(update(...args)),
      remove: (...args) => dispatch(remove(...args)),
      markAllAsUnpacked: (...args) => dispatch(markAllAsUnpacked(...args)),
    };
  }, [dispatch]);

  return actions;
};
```

In `Application` is as simple as this:

```ts
const { add, remove, update, markAllAsUnpacked } = useItemActions(dispatch);
```

## Using Redux's `bindActionCreators`

If you don't even want to do that by hand, you can totally just borrow a utility function like Redux's `bindActionCreators` that will map over an object of your action creators and bind them to `dispatch`.

```ts
import { useMemo } from 'react';
import { bindActionCreators } from '@reduxjs/toolkit';
import { add, update, remove, markAllAsUnpacked } from './reducer';

export const useItemActions = (dispatch) => {
  const actions = useMemo(() => {
    console.log('useItemActions');
    return bindActionCreators(
      { add, update, remove, markAllAsUnpacked },
      dispatch,
    );
  }, [dispatch]);

  return actions;
};
```

## Conclusion

Regardless of which approach you choose, you can have an API closer to what we started with and use a reducer. As you add actions and functionality, the creators will automatically be memoized.