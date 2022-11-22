---
repository: https://github.com/stevekinney/packing-list
branch: with-typescript
endBranch: typescript-context-api-implementation
---

Alright, you can see I did a little bit of the basic set up for you as this is a course on TypeScript and not the Context API itself and you just watched me set up the Context API in a previous example. You're more than welcome to delete `src/context.tsx`, if you'd like.

Let's port over our state management from `src/components/application.tsx`:

```tsx
export const ItemsProvider = ({ children }: PropsWithChildren) => {
  const [items, setItems] = useState<Item[]>(getInitialItems());

  const add = (name: string) => {
    const item = createItem(name);
    setItems([...items, item]);
  };

  const update = (id: string, updates: any) => {
    setItems(updateItem(items, id, updates));
  };

  const remove = (id: string) => {
    setItems(removeItem(items, id));
  };

  const unpackedItems = filterItems(items, { packed: false });
  const packedItems = filterItems(items, { packed: true });

  const markAllAsUnpacked = () => {
    return setItems(items.map((item) => ({ ...item, packed: false })));
  };

  return <ItemsContext.Provider value={null}>{children}</ItemsContext.Provider>;
};

export default ItemsProvider;
```

## Putting together our state management package

Let's bundle up all of those actions and lists into one nice little package.

```ts
const value = {
  items,
  packedItems,
  unpackedItems,
  add,
  remove,
  update,
  markAllAsUnpacked,
};
```

Now, this time around, we chose to use `useState` instead of `useReducer`, so it's a little tricker. There are a bunch of tricks, we _could_ do, but I'm going to use this as excuse to remind you my favorite trick: _Use TypeScript to teach yourself about TypeScript._

What happens if we hover over the `value` we just made?

![](_attachments/Pasted%20image%2020221114142019.png)

Oh, look! That seems like a reasonable type that we can use.

```ts
type ItemsState = {
  items: Item[];
  packedItems: Item[];
  unpackedItems: Item[];
  add: (name: string) => void;
  remove: (id: string) => void;
  update: (id: string, updates: any) => void;
  markAllAsUnpacked: () => void;
};
```

## Assert the type

Alright, here is where we wanted to try out the strategy that I previously showed you because we told `ItemsContext` that it's value would be `null` and—clearly—that's a lie.

```tsx
export const ItemsContext = createContext({} as ItemsState);
```

Don't forget actually pass in that value:

```tsx
<ItemsContext.Provider value={value}>{children}</ItemsContext.Provider>
```

## Wiring it all up

Now, you can take my word that this all works or we can throw it together real fast. It's up to you.

The nice part is that TypeScript isn't really going to let us keep any loose ends around as we do this refactor.

(**Note**: I am _only_ going to do the `ItemList` components, there isn't much of a gain in doing it to `NewItem`, `Header`, and `MarkAllAsUnpacked`.)

In `src/components/application.tsx`, you'll notice that sweet, sweet autocomplete as I pull properties off of the `ItemsContext`.

Next up, `ItemsList` doesn't need those actions at all. We'll remove them and head down to `Item`—following the compiler errors along the way.

## End Result

In `src/components/application.tsx`:

```diff
@@ -1,56 +1,21 @@
-import { useState } from 'react';
-import {
-  createItem,
-  filterItems,
-  getInitialItems,
-  removeItem,
-  updateItem,
-} from '../lib/items';
+import { useContext } from 'react';
+import { ItemsContext } from '../context';
 import Header from './header';
 import ItemList from './item-list';
 import MarkAllAsUnpacked from './mark-all-as-unpacked';
 import NewItem from './new-item';

 const Application = () => {
-  const [items, setItems] = useState(getInitialItems());
-
-  const add = (name: string) => {
-    const item = createItem(name);
-    setItems([...items, item]);
-  };
-
-  const update = (id: string, updates: any) => {
-    setItems(updateItem(items, id, updates));
-  };
-
-  const remove = (id: string) => {
-    setItems(removeItem(items, id));
-  };
-
-  const unpackedItems = filterItems(items, { packed: false });
-  const packedItems = filterItems(items, { packed: true });
-
-  const markAllAsUnpacked = () => {
-    return setItems(items.map((item) => ({ ...item, packed: false })));
-  };
+  const { items, unpackedItems, packedItems, add, markAllAsUnpacked } =
+    useContext(ItemsContext);

   return (
     <main className="flex flex-col gap-8 p-8 mx-auto lg:max-w-4xl">
       <Header items={items} />
       <NewItem addItem={add} />
       <section className="flex flex-col gap-8 md:flex-row">
-        <ItemList
-          title="Unpacked Items"
-          items={unpackedItems}
-          update={update}
-          remove={remove}
-        />
-        <ItemList
-          title="Packed Items"
-          items={packedItems}
-          update={update}
-          remove={remove}
-        />
+        <ItemList title="Unpacked Items" items={unpackedItems} />
+        <ItemList title="Packed Items" items={packedItems} />
       </section>
       <MarkAllAsUnpacked onClick={markAllAsUnpacked} />
     </main>
```

In `src/components/item-list.tsx`:

```diff
@@ -6,11 +6,9 @@ import Item from './item';
 type ItemsProps = {
   title: string;
   items: Item[];
-  update: (id: string, updates: any) => void;
-  remove: (id: string) => void;
 };

-const ItemList = ({ title = 'Items', items, update, remove }: ItemsProps) => {
+const ItemList = ({ title = 'Items', items }: ItemsProps) => {
   const [filter, setFilter] = useState('');
   const id = toKebabCase(title);

@@ -32,7 +30,7 @@ const ItemList = ({ title = 'Items', items, update, remove }: ItemsProps) => {
       </header>
       <ul className="flex flex-col gap-2">
         {filteredItems.map((item) => (
-          <Item key={item.id} item={item} update={update} remove={remove} />
+          <Item key={item.id} item={item} />
         ))}
       </ul>
       {isEmpty && <p className="text-primary-400">(Nothing to show.)</p>}
```

In `src/components/item.tsx`:

```diff
@@ -1,14 +1,14 @@
 import clsx from 'clsx';
-import { useState } from 'react';
+import { useContext, useState } from 'react';
+import { ItemsContext } from '../context';

 type ItemProps = {
   item: Item;
-  update: (id: string, updates: any) => void;
-  remove: (id: string) => void;
 };

-const Item = ({ item, update, remove }: ItemProps) => {
+const Item = ({ item }: ItemProps) => {
   const [editing, setEditing] = useState(false);
+  const { update, remove } = useContext(ItemsContext);

   return (
     <li className="flex items-center gap-2">
```
