`React.memo` is an example of a higher-order component. It's been around since React 16.6.

The TL;DR on `React.memo` is that if you're just going to get the same result, then let's cut out the middle man and just give you what we got last time. On one hand, not figuring out the result seems faster than doing it. On the other hand, you're checking and that's doing stuff. The name of the game is trying to figure out which option involves doing *less stuff*™.

Generally speaking, components in React re-render whenever their parent re-renders. The reconciler works it way down the tree in order to figure out what changed since the last time it rendered your application.

At the end of the day, your component is a function. "Rendering" is a fancy way of saying that React calls your function. If the output is different than last time, it makes a note of what the changes are in order to update the DOM to reflect these changes.

With `React.memo`, we're basically finding a way to say to React that there is nothing to see here and to move along.

`React.memo` allows you to skip rerendering a component when the props it recieves are the same as the the last time that it rendered. Components that use `React.memo` should always have [pure rendering logic](React.memo.md).

When we use `React.memo`, we are basically making React a promise that in our implementation, as long as it gets the same props, it will also return the same output. So, when in doubt, you can skip even bothering to see if we should re-render this component.

Keep mind that even if you memoize a component, that will only prevent a rerender if the props are the same, if the component updates its own state, then it _will_ re-render. If it's using a context and that context changes, it will also re-render. If this is a concern, you can always break your component apart. Let the outer component subscribe to changes to the context and then the child component can use `React.memo`.

If you're store an object in state, consider passing the values in as individual props. This helps you avoid any issues with shallow equality by using primitive values.

Here is a set of criteria for determining if `React.memo` is worth your time.

- You've got a pure, functional component on your hands.
- It re-renders super often.
- And when it re-renders, it's typically get the exact same props.
- It's big enough that re-rendering so often feels problematic.

And just to be fair and balanced, let's whip up a list of times when you should _not_ memoize a component:

- It's getting new props all of the time. So, you're waisting your time checking and memoizing.
- It's small enough that honestly, it doesn't provide any benefit.
- You're wrapping a class-based component. Extend `React.PureComponent` instead or get fancy with `shouldComponentUpdate()`.

## A Caveat with `React.memo`

So, one of the problems here is that React is generally-speaking going to check if objects are equally by checking to see if they have the same reference. So, anything that spits out a new array, function, or object is going to break our `React.memo` force field almost instantaneously.

Also, like keep in mind that if you memoize a component and it doesn't re-render as a result, then none of its children will re-render as a result. This is _probably_ what you want, but if you just start memoizing everything with reckless abandon, it's probably on a matter of time until you run into a bug.

This is where we might choose to reach from a deadly combination of `React.memo` and `useMemo`.

If one of your props in an anonymous function, then memoization is a waste of time as your props are going to be different every time. (You can probably get around this using [`useCallback`](useMemo%20and%20useCallback.md).)

- We would use `useMemo` to remember the last value that it produced when receiving the the same inputs.
- This means we'd get _referentially_ the same array as the last time we called that function.
- And this would get `React.memo` doing its thing again.
- An alternate approach would be to have it take individual, primitive values, but that's not always something that you can get away with.

## A word on over-optimization

The game we're playing is we're aiming that when we use memoizing is that the work of doing the comparison will save us the work of re-rendering. But, I feel like I should point out that the act of comparing the previous props with the new props isn't free and if it's consistently wasted, then we only added to the problem because doing stuff is slower than not doing stuff and doing extra stuff to try to avoid doing stuff and then doing the original stuff anyway is just doing extra stuff.

## Alternatives to using `React.memo`

- If this a component that is just visually wraps other components, consider using children instead. React will not re-render these children when the wrapper component updates its state.
- Don't [lift state up any more than you need to](Pushing%20state%20down.md). Parents trigger re-renders on their children, but the opposite is _not_ true.
- Avoid unnecessary effects and remove unnecessary dependency from your calls to `useEffect`.

## Using a Custom Comparison Function

`React.memo` takes a second argument, a function that you can use to check to see if the old props and the new props are equal.

1. Make sure you comparison function isn't slower than the re-render was to begin with.
2. Make sure you check those functions since the old ones might have stale data in a closure.
