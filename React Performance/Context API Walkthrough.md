If we think about it, it's a little silly that `ItemsList` needs to take `update` and `remove` or even `dispatch`. It's not doing anything with it. It's just handing it off to `Item`.

Couldn't we use use the Context API to avoid this unnecessary prop drilling?

I'll spare you the tedium of refactoring to the Context API, that's better covered elsewhere. Instead, you can check out `$branch`.

## A regression

Whoa, we ported over all of our optimizations and yet—we're back at square one. If you remember, React components rerender for one of three reasons:

- The state changed.
- It's parent re-renders (and we didn't opt out with `React.memo`)
- The context changed.

Oh, wait. Look at that last one. I guess we opted back _into_ re-renders.

When I say a context, that triggers a re-render, that's because it's basically following the same rules as triggering a state change. If we think about how we set this up, our context is a parent over the entire application tree and thereby we're following the rules we established at the very beginning of our time together.

## Separating out actions

All of our actions are memoized, but it doesn't really matter becausse even if they don't change, the object that we pass to value is recreated every time items change—and that's pretty often. We're basically in a super similar place to where we were when we first started this journey.

But, what we had _two_ contexts? One for stuff that changed and one for stuff that _didn't_?

## Creating an `ItemsActionContext`

We know that we count anything that relies on `dispatch` to be stable. So why don't we pull those out into their context?

```ts
const ItemsActionContext = createContext();
```

And then we can wrap our _other_ provider in our new context provider.

```ts
const ItemsProvider = ({ children }) => {
  const [items, dispatch] = useReducer(reducer, getInitialItems());
  const actions = useItemActions(dispatch);

  const unpackedItems = filterItems(items, { packed: false });
  const packedItems = filterItems(items, { packed: true });
  const count = items.length;

  return (
    <ItemsActionContext.Provider value={actions}>
      <ItemsContext.Provider
        value={{
          unpackedItems,
          packedItems,
          count,
        }}
      >
        {children}
      </ItemsContext.Provider>
    </ItemsActionContext.Provider>
  );
};
```

One thing I want to point is that we want to put the one that _doesn't_ change as the parent to the one that _does_ change since parents cause their childrent to re-render.

## Creating a new hook

We don't really want anyone accessing `ItemsActionContext` outside. Instead, we'll give ourselves a hook called `useActions` that we can use in our components.

```ts
export const useActions = () => {
  const actions = useContext(ItemsActionContext);
  return actions;
};
```

### Add in our new hook

Now, we need to use this new hook everywhere. It's less tedious than you think.

In `a/src/components/item.js`:

```diff
@@ -1,10 +1,10 @@
 import clsx from 'clsx';
-import { memo, useContext, useState } from 'react';
-import { ItemsContext } from '../contexts/items';
+import { memo, useState } from 'react';
+import { useActions } from '../contexts/items';

 const Item = ({ item }) => {
   const [editing, setEditing] = useState(false);
-  const { update, remove } = useContext(ItemsContext);
+  const { update, remove } = useActions();

   return (
     <li className="flex items-center gap-2">
```

In `a/src/components/mark-all-as-unpacked.js`:

```diff

+++js
@@ -1,8 +1,7 @@
-import { useContext } from 'react';
-import { ItemsContext } from '../contexts/items';
+import { useActions } from '../contexts/items';

 const MarkAllAsUnpacked = () => {
-  const { markAllAsUnpacked } = useContext(ItemsContext);
+  const { markAllAsUnpacked } = useActions();

   return (
     <div className="mb-16">
```

In `a/src/components/new-item.js b/src/components/new-item.js`:

```diff
@@ -1,9 +1,9 @@
-import { memo, useContext, useState } from 'react';
-import { ItemsContext } from '../contexts/items';
+import { memo, useState } from 'react';
+import { useActions } from '../contexts/items';

 const NewItem = () => {
   const [newItemName, setNewItemName] = useState('');
-  const { add } = useContext(ItemsContext);
+  const { add } = useActions();

   return (
     <form
```

## Drawing a line with the count

It's a little silly, I know. But, `count` is still giving us a problem. Could you make _another_ context? Sure. You could. But, don't get carried away here.

You're already calling the hook in `Application`. Just pass it through as a prop. Don't get fancy.

In `/src/components/application.js`:

```diff
@@ -6,11 +6,11 @@ import MarkAllAsUnpacked from './mark-all-as-unpacked';
 import NewItem from './new-item';

 const Application = () => {
-  const { packedItems, unpackedItems } = useContext(ItemsContext);
+  const { packedItems, unpackedItems, count } = useContext(ItemsContext);

   return (
     <main className="flex flex-col gap-8 p-8 mx-auto lg:max-w-4xl">
-      <Header />
+      <Header count={count} />
       <NewItem />
       <section className="flex flex-col gap-8 md:flex-row">
         <ItemList title="Unpacked Items" items={unpackedItems} />
```

In `/src/components/header.js`:

```diff
@@ -1,9 +1,6 @@
-import { memo, useContext } from 'react';
-import { ItemsContext } from '../contexts/items';
-
-const Header = () => {
-  const { count } = useContext(ItemsContext);
+import { memo } from 'react';

+const Header = ({ count }) => {
   return (
     <header id="page-header">
       <h1 className="text-2xl font-bold">Packing List</h1>
```
