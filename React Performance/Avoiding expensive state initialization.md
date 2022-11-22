Let's say that you have some expensive function that you need to call in order to initialize your component or application.

```js
const expensiveThing = getExpensiveThing();
const [state, setState] = useState(expensiveThing);
```

Alternatively, you might even try to do something like this:

```js
const [state, setState] = useState(getExpensiveThing());
```

It's the same basic idea.

But, remembver that your component is a function that is called every render. This means you're going to call `getExpensiveThing()` every time. And you know what? It's expensive.

An alternative approach is to pass the `useState` a function to tell it how to initialize itself the first time.

```js
const [state, setState] = useState(() => getExpensiveThing());
```

`useState` will call that function the first time it sets up `state` and `setState` and that's it.
