Well what do we do? We _can't_ do this:

```ts
type ItemsState = {
  // …
  update: (id: string, updates: Item) => void;
  // …
};
```

I immediately get yelled at in `Item.tsx`.

![](_attachments/Pasted%20image%2020221114144618.png)

In fairness, it's not wrong. That's only a _part_ of an `Item`.

It's time for a utility type!

## Using `Partial<T>`

```ts
type ItemsState = {
  items: Item[];
  packedItems: Item[];
  unpackedItems: Item[];
  add: (name: string) => void;
  remove: (id: string) => void;
  update: (id: string, updates: Partial<Item>) => void;
  markAllAsUnpacked: () => void;
};
```

In the implementation:

```ts
const update = (id: string, updates: Partial<Item>) => {
  setItems(updateItem(items, id, updates));
};
```
