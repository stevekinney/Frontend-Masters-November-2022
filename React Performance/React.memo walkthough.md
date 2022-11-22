![](<_attachments/React%20Memo%20(Start).gif>)

Okay, sure typing in the input field is not so bad anymore, but everything else is still causing the world to re-render. The most egregious of which is if you squint and look just below the page title, we're re-rendering the total number of items—even when that number isn't changing. Literally nothing about that component is changing—and yet, we keep re-rendering it.

`React.memo` should be able to help here, right? Let's give it a shot. Let's refactor our `Header` component in `header.js` as follows:

```ts
import { memo } from 'react';

const Header = ({ items }) => (
  <header id="page-header">
    <h1 className="text-2xl font-bold">Packing List</h1>

    <p id="number-of-items">
      {items.length} {items.length === 1 ? 'item' : 'items'}
    </p>
  </header>
);

export default memo(Header);
```

We can add a little bit of introspection to our component by adding in our own comparison function in `header.js`.

```ts
export default memo(Header, (previous, current) => {
  const isDifferent = Object.is(previous, current);
  console.log({ previous, current, isDifferent });
  return isDifferent;
});
```

If we look at our console, we'll see the following:

```json
{
  "previous": {
    "items": Array(23)
  },
  "current": {
    "items": Array(23)
  },
  "isDifferent": false
}
```

The count is the same, but because React discourages us from mutating objects, we've replaced the item with an entirely new array when we modified an object. This mean, that when `React.memo` checks in, it's working with two fundamentally different arrays.

We could do some dark magic to get the effect we're looking for.

```ts
export default memo(Header, (previous, current) => {
  const isDifferent = previous.items.length === current.items.length;
  console.log({
    previous: previous.items.length,
    current: current.items.length,
    isDifferent,
  });
  return isDifferent;
});
```

But, as they say in _Jurassic Park_: just because we _can_ do something does not mean that we _should_. Since we only care about the number anyway and primitives like numbers are equal to each other (e.g. they're compared by value instead of reference), it should work just fine without a custom comparison function.

First and foremost, let's get rid of that dark experiment in `header.js`.

```ts
export default memo(Header);
```

If we want to display a number, we should just pass in a number.

We'll update our component as follows:

```ts
import { memo } from 'react';

const Header = ({ count }) => (
  <header id="page-header">
    <h1 className="text-2xl font-bold">Packing List</h1>
    <p id="number-of-items">
      {count} {count === 1 ? 'item' : 'items'}
    </p>
  </header>
);

export default memo(Header);
```

We need to fix things up in `Application` to reflect this change.

```tsx
<Header count={items.length} />
```

And with that, we should see that our header only updates when we add a new item.

![](<_attachments/React%20Memo%20(Complete).gif>)

## What Changed?

In `src/components/application.js`:

```diff
   return (
     <main className="flex flex-col gap-8 p-8 mx-auto lg:max-w-4xl">
-      <Header items={items} />
+      <Header count={items.length} />
       <NewItem addItem={add} />
       <section className="flex flex-col gap-8 md:flex-row">
         <ItemList
```

In `src/components/header.js`:

```diff
+import { memo } from 'react';
+
+const Header = ({ count }) => (
   <header id="page-header">
     <h1 className="text-2xl font-bold">Packing List</h1>
     <p id="number-of-items">
-      {items.length} {items.length === 1 ? 'item' : 'items'}
+      {count} {count === 1 ? 'item' : 'items'}
     </p>
   </header>
 );

-export default Header;
+export default memo(Header);
```
