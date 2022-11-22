This will be quick, but we'll build a little break into it.

Are you familiar with the [`tap` utility method](https://lodash.com/docs/#tap)? `tap` takes an argument and a function. It passes the argument into the function and immediately returns the return value.

For example:

```js
const arrayWithoutLast = tap([1, 2, 3, 4], function (array) {
  // Pop always returns the value it removed from the end of the array.
  return array.pop();
});
```

So, here are the nuances.

It not only needs to figure out the type like `identity` but it also needs to pass that same type into the function, *and* it needs to return that type. The callback function is just mutating the object, so it doesn't need to return anything.

The following code should return the original array without the last item.

```ts
const popped = tap([1, 2, 3], (n) => n.pop());
```

(You can peek at the solution [here](https://gist.github.com/stevekinney/d14cbaff3e0aa8ee3e1dcf96837af1ca)).