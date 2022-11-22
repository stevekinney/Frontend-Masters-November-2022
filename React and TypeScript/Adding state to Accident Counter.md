---
repository: https://github.com/stevekinney/accident-counter
branch: main
---

Let's quickly add some state to a simple little counter. For starters, this is basically the entire implementation.

```tsx
const Counter = () => {
  return (
    <section className="flex flex-col items-center w-2/3 gap-8 p-8 bg-white border-4 shadow-lg border-primary-500">
      <h1>Days Since the Last Accident</h1>
      <p className="text-6xl">0</p>
      <div className="flex gap-2">
        <button>➖ Decrement</button>
        <button>🔁 Reset</button>
        <button>➕ Increment</button>
      </div>
      <div>
        <form onSubmit={(e) => e.preventDefault()}>
          <input type="number" value={0} />
          <button type="submit">Update Counter</button>
        </form>
      </div>
    </section>
  );
};

export default Counter;
```

This is nothing particularly special to see here. Adding some state can be done swiftly using the `useState` hook.

```tsx
const [count, setCount] = useState(0);
```

And then—yea—we'll add some state to our counter, just like every other modern React tutorial you've ever seen.

```diff
@@ -1,12 +1,16 @@
+import { useState } from 'react';
+
 const Counter = () => {
+  const [count, setCount] = useState(0);
+
   return (
     <section className="flex flex-col items-center w-2/3 gap-8 p-8 bg-white border-4 shadow-lg border-primary-500">
       <h1>Days Since the Last Accident</h1>
-      <p className="text-6xl">0</p>
+      <p className="text-6xl">{count}</p>
       <div className="flex gap-2">
-        <button>➖ Decrement</button>
-        <button>🔁 Reset</button>
-        <button>➕ Increment</button>
+        <button onClick={() => setCount(count - 1)}>➖ Decrement</button>
+        <button onClick={() => setCount(0)}>🔁 Reset</button>
+        <button onClick={() => setCount(count + 1)}>➕ Increment</button>
       </div>
       <div>
         <form onSubmit={(e) => e.preventDefault()}>
```

We'll worry about the form in a hot minute.

## Taking a look at what TypeScript has figured out for us

So, this is where it gets kind of cool. This file doesn't look like TypeScript, but it _is_.

If you hover over `count`, you'll see that TypeScript was about to deduce that it's a number because you set its initial value to a number.

```ts
const count: number;
```

You can also see that it figured out that `setCount` should _only_ take a number, which means that `count` will _always_ be a number.

```ts
const setCount: React.Dispatch<React.SetStateAction<number>>;
```

Don't worry about all of that ceremony. `useState` is an abstraction over `useReducer`, which works by dispatching actions.

If we try to break the rules, you'll see that TypeScript keeps us honest.

![](_attachments/Pasted%20image%2020221107062434.png)

This is _particulary_ helpful when dealing with one of my least favorite parts of the browser: the fact that number inputs store their values as strings. 🙄

Let's take this head on and try to [get that little form for manually setting the count working](useState,%20an%20exercise.md).