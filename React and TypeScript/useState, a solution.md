---
repository: https://github.com/stevekinney/accident-counter
branch: main
endBranch: form-solution
---

Let's [add some functionality to that counter](useState,%20an%20exercise.md).

We'll start by creating some state to hold on to the value in the input field. I'm going to base it on whatever the current count was when the component was first rendered, but you don't have to.

```tsx
const [draftCount, setDraftCount] = useState(count);
```

I can _try_ to add that as a change handler to the `<input />`, but I already alluded to the fact that this isn't going to go particularly well.

![](_attachments/Pasted%20image%2020221107064059.png)

My annoyance with this behavior of the DOM may or may not become a recurring theme today.

This is similar to what we saw before. The message reads:

> Argument of type `string` is not assignable to parameter of type `SetStateAction<number>`.

The solution is straight-forward here. We need to coerce that value to a number. Choose your poison. You can use `parseInt`, `Number()`, or the `+` unary.

```ts
setDraftCount(+e.target.value);
setDraftCount(parseInt(e.target.value));
setDraftCount(Number(e.target.value));
```

Now, we just update the _real_ count to the draft count when we submit the form.

```ts
<form
  onSubmit={(e) => {
    e.preventDefault();
    setCount(draftCount);
  }}
>
  // …
</form>
```

You'll see that this almost works. That input field is hard to clear out. An empty string coerces to zero. This isn't TypeScript's fault per se, but it's an issue that we've run into because TypeScript is keeping us honest and making it harder to play fast and loose.

We have a few options:

- We can store the draft state and convert it to a number when it's time to set the count. This is easy and we should totally do it.
- Even better: we can let `draftCount` be either a string or a number and then do that first bullet point.

This _seems_ tedious, but I will remind you that it's actually correct probably what we should be doing regardless.

If we default it to a string, it's now a string.

```ts
const [draftCount, setDraftCount] = useState('0');
```

The reason I don't love this is because I have to think about it if I want it to default to whatever the count is. Like, if I wanted to use a side effect to update the draft count in the input field whenever they hit the "Increment" or "Decrement" buttons, I'm burdened with coercing it back into a string.

This is particularly annoying because input fields don't care. Just look at it's type.

```ts
React.InputHTMLAttributes<HTMLInputElement>.value?: string | number | readonly string[] | undefined
```

Amongst other things, it takes a `string | number`. We'll deal with that in a moment. Let's coerce that string into a number for the count, which totally needs to be a number before we forget.

```tsx
<form
  onSubmit={(e) => {
    e.preventDefault();
    setCount(Number(draftCount));
  }}
>
  // …
</form>
```

We were taking advantage of TypeScript automatically figuring stuff out on our behalf, but the code we wrote is _really_ just similar to this:

```ts
const [count, setCount] = useState<number>(0);
const [draftCount, setDraftCount] = useState<string>('0');
```

We can take things into our own hands and update our draft state to support being either a string or a number.

```ts
const [draftCount, setDraftCount] = useState<string | number>(0);
```

Now, everyhing works as expected.

## Extension: updating the draft count

Just to make this all worth it, we can add that little extra feature we were talking about where the input field is updated to whatever the current count is.

```js
useEffect(() => {
  setDraftCount(count);
}, [count]);
```

Look ma, no typing. Everything just works. Here is a fun fact that you can impress people at parties, there is an alternative take on this that _will_ upset TypeScript.

```ts
// Reset the counter after a certain amount of time
useEffect(() => setTimeout(setCount(0), 31536000000));
```

The error message reads:

> Type `Timeout` is not assignable to type `void | Destructor`.

`useEffect` expects the function you hand it to return either nothing or a function that it should call when the component is unmounting. There is code inside of React to defensively protect you from giving it something else. We've probably all written code like this before:

```ts
if (typeof fn === 'function') fn();
```

But, TypeScript's job is to try to reduce this burden on us. So, it's not even going to let you do that to React. Luckily, this fix is simple. Add back those curly braces to the function so that it doesn't automatically return a value.

```ts
// Reset the counter after a certain amount of time
useEffect(() => {
  setTimeout(setCount(0), 31536000000);
});
```

We didn't run into that problem with `setDraftCount` because `setDraftCount` returns `void` (e.g. `undefined`) anyway.

### A quick disclaimer

React experts will know that using `useEffect` isn't great here because it could trigger a second render. One alternative is to just wrap `setCount` to do both at the same time. This is probably the best example, but I'm most just trying to drum up fictitious reasons to show you the `useEffect` hook with TypeScript and it seemed more efficient to do it here than to set us up in a brand new example.
