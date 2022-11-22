We still have more work to do. Let me tell you about the next thing that's annoying me. Whenever I add a new item, not only do both lists update despite the fact that I'm only adding an unpacked item. But every single item is re-rendering despite the fact that it hasn't changed in the slightest.

![](<_attachments/useCallback%20(Start).gif>)

Let's go into `ItemList` and pop a `React.memo` on there—for science.

```diff
@@ -1,4 +1,4 @@
-import { useState } from 'react';
+import { memo, useState } from 'react';
 import { filterItems } from '../lib/items';
 import { toKebabCase } from '../lib/kebab-case';
 import Item from './item';
@@ -45,4 +45,4 @@ const ItemList = ({ title = 'Items', items, update, remove }) => {
   );
 };

-export default ItemList;
+export default memo(ItemList);
```

And that's not all. For some reason, `NewItem` is changing when we toggle the status of an item? That's not cool. Let's toss one there too.

```diff
-import { useState } from 'react';
+import { memo, useState } from 'react';

 const NewItem = ({ addItem }) => {
   const [newItemName, setNewItemName] = useState('');
@@ -38,4 +38,4 @@ const NewItem = ({ addItem }) => {
   );
 };

-export default NewItem;
+export default memo(NewItem);
```

Things should be better now, right?

No dice. How come? Well. Remember about how two arrays aren't the same? The same is true for functions and we we're re-creating these functions on ever render.

```ts
const add = (name) => {
  const item = createItem(name);
  setItems([...items, item]);
};

const update = (id, updates) => {
  setItems(updateItem(items, id, updates));
};

const remove = (id) => {
  setItems(removeItem(items, id));
};

const unpackedItems = filterItems(items, { packed: false });
const packedItems = filterItems(items, { packed: true });

const markAllAsUnpacked = () => {
  return setItems(items.map((item) => ({ ...item, packed: false })));
};
```

That's an anonymous function each an every time and it's a different object in memory from the last time we whipped up a function.

But, things get a little bit trickier. We _could_ try to wrap these functions in `useCallback`.

```ts
const add = useCallback(
  (name) => {
    const item = createItem(name);
    setItems([...items, item]);
  },
  [setItems, items],
);
```

It technically works, but it doesn't exactly do us a lot of good. The problem is that `items` is really the only thing we're ever dealing with in this application and it's basically changing _every_ time we do anything—especially now that we've decided to move that `newItemName` and `setNewItemName` into `NewItem` where they belong.

So, what are going to do? We'll it turns out that the `SetStateAction` in React actually takes two different kinds of arguments.

```ts
type SetStateAction<S> = S | ((prevState: S) => S);
```

This means, that you could do the following:

```ts
const [count, setCount] = useState(2);

const double = count * 2;
setCount(double);
```

Or, you could to this:

```ts
const [count, setCount] = useState(2);

setCount((count) => count * 2);
```

React will automatically pass the current version that you were working with when it goes to update the state. This mean, you don't need to pass a reference to it.

Let's look at how this would work with the `add` function.

```ts
const add = useCallback(
  (name) => {
    const item = createItem(name);
    setItems((previousItems) => [...previousItems, item]);
  },
  [setItems],
);
```

But, wait—there is more. It turns out that the `SetStateAction` _is_ the same object between renders. This means that `setItems` isn't a different object in memory each time.

And, since we wrapped `add` with `useCallback` and now it's only dependency is `setItems`, it isn't changing either.

Oh, look. `NewItem` is no longer re-rendering!

![](<_attachments/useCallback%20(Complete).gif>)

## Exercise

- Can you make the same change to `update`, `remove`, and `markAllAsUnpacked`?
- See if you can stop `MarkAllAsUnpacked` from re-rendering so much, while you're at it.

You can check out the solution here: [Previous state and useCallback (Solution)](Previous%20state%20and%20useCallback%20(Solution).md).

## What changed in this section?

```diff
diff --git a/src/components/application.js b/src/components/application.js
index 626749e..916bff4 100644
--- a/src/components/application.js
+++ b/src/components/application.js
@@ -1,4 +1,4 @@
-import { useState } from 'react';
+import { useCallback, useState } from 'react';
 import {
   createItem,
   filterItems,
@@ -14,10 +14,13 @@ import NewItem from './new-item';
 const Application = () => {
   const [items, setItems] = useState(getInitialItems());

-  const add = (name) => {
-    const item = createItem(name);
-    setItems([...items, item]);
-  };
+  const add = useCallback(
+    (name) => {
+      const item = createItem(name);
+      setItems((previousItems) => [...previousItems, item]);
+    },
+    [setItems],
+  );

   const update = (id, updates) => {
     setItems(updateItem(items, id, updates));
diff --git a/src/components/item-list.js b/src/components/item-list.js
index df3c85c..7021405 100644
--- a/src/components/item-list.js
+++ b/src/components/item-list.js
@@ -1,4 +1,4 @@
-import { useState } from 'react';
+import { memo, useState } from 'react';
 import { filterItems } from '../lib/items';
 import { toKebabCase } from '../lib/kebab-case';
 import Item from './item';
@@ -45,4 +45,4 @@ const ItemList = ({ title = 'Items', items, update, remove }) => {
   );
 };

-export default ItemList;
+export default memo(ItemList);
diff --git a/src/components/item.js b/src/components/item.js
index de0b079..298b289 100644
--- a/src/components/item.js
+++ b/src/components/item.js
@@ -1,5 +1,5 @@
 import clsx from 'clsx';
-import { useState } from 'react';
+import { memo, useState } from 'react';

 const Item = ({ item, update, remove }) => {
   const [editing, setEditing] = useState(false);
@@ -45,4 +45,4 @@ const Item = ({ item, update, remove }) => {
   );
 };

-export default Item;
+export default memo(Item);
diff --git a/src/components/new-item.js b/src/components/new-item.js
index d1426b3..5d3f130 100644
--- a/src/components/new-item.js
+++ b/src/components/new-item.js
@@ -1,4 +1,4 @@
-import { useState } from 'react';
+import { memo, useState } from 'react';

 const NewItem = ({ addItem }) => {
   const [newItemName, setNewItemName] = useState('');
@@ -38,4 +38,4 @@ const NewItem = ({ addItem }) => {
   );
 };

-export default NewItem;
+export default memo(NewItem);

```
