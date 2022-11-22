There is another strategy that we can take here. `useCallback` is a great performance optimization, but it's not exactly free. (And, like I always say—you shouldn't really worry about stuff like that until after you measure anyway). We could just take a different approach, however, and side-step around all of that work we did previously.

`useState` is just a wrapper around `useReducer`. And, just like `SetStateAction` remains the same object between renders, so does `dispatch`.

## Refactoring to a Reducer

Let's go on a little journey and see how we can get the same optimizations without needing to use `useCallback`.

I did a little bit of the work for you and wrote up a reducer and some action creators in `src/lib/reducer.ts`. I wrote it in TypeScript and used a helper from [Redux Toolkit](https://redux-toolkit.js.org) for my sanity, but here is what it would look like in regular ol' JavaScript with out the helpers, if you're interested.

```js
import { createItem, removeItem, updateItem } from './items';

export const add = (name) => ({
  type: 'items/add',
  payload: { name },
});

export const update = (id, properties) => {
  return { type: 'items/update', payload: { id, ...properties } };
};

export const remove = (id) => ({
  type: 'items/remove',
  payload: { id },
});

export const markAllAsUnpacked = () => ({ type: 'items/mark-all-as-unpacked' });

export const reducer = (items, action) => {
  if (action.type === 'items/add') {
    const item = createItem(action.payload.name);
    return [...items, item];
  }

  if (action.type === 'items/update') {
    const { id, ...properties } = action.payload;
    return updateItem(items, id, properties);
  }

  if (action.type === 'items/remove') {
    return removeItem(items, action.payload.id);
  }

  if (action.type === 'items/mark-all-as-unpacked') {
    return items.map((item) => ({ ...item, packed: false }));
  }

  return items;
};
```

The compelling part here is that just like using the previous state in our `SetStateAction` callback, we're eliminating the need to hold on to a reference to our application's state.

As an added bonus, we are just sending messages (e.g. actions) to the reducer and those actions are really just plain ol' JavaScript objects.

### Swapping in the reducer to manage state

We're going to remove `useState` and switch over to `useReducer`.

```diff
 const Application = () => {
-  const [items, setItems] = useState(getInitialItems());
+  const [items, dispatch] = useReducer(reducer, getInitialItems());
```

Now, if you save, you'll see that our application won't compile because we're referencing `setItems` a bunch and that no longer exists. So, let's get rid of those functions that we optimized previously.

```diff
 const Application = () => {
   const [items, dispatch] = useReducer(reducer, getInitialItems());

-  const add = useCallback(
-    (name) => {
-      const item = createItem(name);
-      setItems((previousItems) => [...previousItems, item]);
-    },
-    [setItems],
-  );
-
-  const update = useCallback(
-    (id, updates) => {
-      setItems((items) => updateItem(items, id, updates));
-    },
-    [setItems],
-  );
-
-  const remove = useCallback(
-    (id) => {
-      setItems((items) => removeItem(items, id));
-    },
-    [setItems],
-  );
-
   const unpackedItems = filterItems(items, { packed: false });
   const packedItems = filterItems(items, { packed: true });

-  const markAllAsUnpacked = useCallback(() => {
-    return setItems((items) =>
-      items.map((item) => ({ ...item, packed: false })),
-    );
-  }, [setItems]);
-
   return (
     <main className="flex flex-col gap-8 p-8 mx-auto lg:max-w-4xl">
       <Header count={items.length} />
```

We're moving forward, but now all of those functions don't exist. Let's keep going and swap them all out with `dispatch`.

```diff
@@ -21,22 +21,20 @@ const Application = () => {
   return (
     <main className="flex flex-col gap-8 p-8 mx-auto lg:max-w-4xl">
       <Header count={items.length} />
-      <NewItem addItem={add} />
+      <NewItem dispatch={dispatch} />
       <section className="flex flex-col gap-8 md:flex-row">
         <ItemList
           title="Unpacked Items"
           items={unpackedItems}
-          update={update}
-          remove={remove}
+          dispatch={dispatch}
         />
         <ItemList
           title="Packed Items"
           items={packedItems}
-          update={update}
-          remove={remove}
+          dispatch={dispatch}
         />
       </section>
-      <MarkAllAsUnpacked onClick={markAllAsUnpacked} />
+      <MarkAllAsUnpacked dispatch={dispatch} />
     </main>
   );
 };
```

Alright, it renders, but definitely don't try to _do_ anything. We'll take that new `dispatch` prop in `ItemList` and pass it through to `Item`.

```diff
@@ -9,7 +9,7 @@ const EmptyState = ({ id, items, filteredItems }) => (
   </p>
 );

-const ItemList = ({ title = 'Items', items, update, remove }) => {
+const ItemList = ({ title = 'Items', items, dispatch }) => {
   const [filter, setFilter] = useState('');
   const id = toKebabCase(title);

@@ -31,7 +31,7 @@ const ItemList = ({ title = 'Items', items, update, remove }) => {
       </header>
       <ul className="flex flex-col gap-2">
         {filteredItems.map((item) => (
-          <Item key={item.id} item={item} update={update} remove={remove} />
+          <Item key={item.id} item={item} dispatch={dispatch} />
         ))}
       </ul>
       {isEmpty && (

```

And now here is where the fun happens, we'll refactor `Item` to dispatch actions instead of call those functions we were using previously. Instead, we'll wrap our action creators with `dispatch`.

```diff
@@ -1,7 +1,8 @@
 import clsx from 'clsx';
 import { memo, useState } from 'react';
+import { update, remove } from '../lib/reducer';

-const Item = ({ item, update, remove }) => {
+const Item = ({ item, dispatch }) => {
   const [editing, setEditing] = useState(false);

   return (
@@ -11,7 +12,7 @@ const Item = ({ item, update, remove }) => {
         className="focus:bg-red-500"
         checked={item.packed}
         id={`toggle-${item.id}`}
-        onChange={() => update(item.id, { packed: !item.packed })}
+        onChange={() => dispatch(update(item.id, { packed: !item.packed }))}
       />
       <label
         htmlFor={`toggle-${item.id}`}
@@ -23,7 +24,9 @@ const Item = ({ item, update, remove }) => {
         value={item.name}
         id={`edit-${item.id}`}
         className={clsx('py-0 text-sm', { hidden: !editing })}
-        onChange={(event) => update(item.id, { name: event.target.value })}
+        onChange={(event) =>
+          dispatch(update(item.id, { name: event.target.value }))
+        }
       />
       <div className="flex gap-2">
         <button
@@ -36,7 +39,7 @@ const Item = ({ item, update, remove }) => {
         <button
           className="px-2 py-0 text-xs"
           aria-label={`Remove "${item.name}"`}
-          onClick={() => remove(item.id)}
+          onClick={() => dispatch(remove(item.id))}
         >
           🗑 Remove
         </button>

```

## What happened here?

We got the same benefits of using `useCallback` but without doing anything other than changing the way we manage state in our application.

## Exercise

There is still some work to do. Can you take the same approach to `NewItem` and `MarkAllAsPacked`?

We'll review the solution in [Refactoring to reducers (Solution)](Refactoring%20to%20reducers%20(Solution).md).
