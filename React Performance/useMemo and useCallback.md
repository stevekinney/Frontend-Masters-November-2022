## useMemo

`useMemo` is a special hook that let's React remember a given value between renders. It takes a function intended to figure out what the value is and a list of dependencies (just like—let's say—`useEffect` does). On each render, React looks at the list of dependencies, none of the values in the array are different, then it skips going through the work of re-calculating that value and just returns whatever it returned last time.

The invokes [Steve's Golden Rule of Performance](Steve's%20Golden%20Rule%20of%20Performance.md): Doing less stuff takes less time.

- `React.memo` is for memozing components. 
- `useMemo` is for memozing values.

Just like `useEffect`, you give `useMemo` a list of dependencies. If that hasn't changed, then why bother recalculating anything.

One good use case is anything that involves a heavy computation time, like finding a prime number or generating a QR code or something. (These are also good candidates for things that you probably _shouldn't_ do on the main thread, but I digress.)


## useCallback

`useCallback` is like `useMemo` but it's for saving a reference to a function between renders. This is useful for when you have to create a function in the component, but you don't want to create a new one every single time that the component re-renders. This is also beneficial when you're trying to use `React.memo` as now it won't instantly break the memoization by slipping in a brand new—and referentially-different—function every time.

This is super useful for stuff like action creators as well.

## Inside Custom Hooks

Since you don't know how often you're hook is going to be used and in what cases it's going to get used, it makes sense to make use of `useCallback` and `useMemo` to optimize the performance of your custom hooks.

## Inside of Context Providers

You might choose to use `useMemo` and/or `useCallback` in a Context provider in order to limit re-renders across the component tree.

## Further Reading

- [ ] [useCallback (reactjs.org)](https://beta.reactjs.org/apis/react/useCallback)
- [ ] [Your Guide to React.useCallback() (dmitripavlutin.com)](https://dmitripavlutin.com/react-usecallback/)
- [ ] [How to useMemo and useCallback: you can remove most of them (developerway.com)](https://www.developerway.com/posts/how-to-use-memo-use-callback)
- [ ] [Understanding useMemo and useCallback (joshwcomeau.com)](https://www.joshwcomeau.com/react/usememo-and-usecallback/)
- [ ] [Learn How to Tame React's useCallback Hook (kinsta.com)](https://kinsta.com/blog/react-usecallback/)
- [ ] [useCallback and useRef: Two React Hooks You Should Learn (telerik.com)](https://www.telerik.com/blogs/usecallback-useref-two-react-hooks-you-should-learn)
- [ ] [All About React useCallback() - Hook API Reference In React (knowledgehut.com)](https://www.knowledgehut.com/blog/web-development/all-about-react-usecallback)
