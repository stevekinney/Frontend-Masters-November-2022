Remember when y'all set up the Context API in that packing list application? Did you notice that I left something nasty in there?

It's hiding out in `src/context.tsx`:

```tsx
/* ---------------------------------- 👇 --- */
const update = (id: string, updates: any) => {
  setItems(updateItem(items, id, updates));
};
```

An `any`! Gross. The only thing that would make that worse is that we copy and pasted that `any` to the `ItemsState` type. I told you, these things spread. I don't even get an error if I change out the first one in the implementation.

```tsx
type ItemsState = {
  items: Item[];
  packedItems: Item[];
  unpackedItems: Item[];
  add: (name: string) => void;
  remove: (id: string) => void;
  /* --------------------------- 👇 ------ */
  update: (id: string, updates: any) => void;
  markAllAsUnpacked: () => void;
};
```

Can you fix my error, please? I'll see you over in the [solution](Deriving%20a%20partial%20type%20for%20updating,%20a%20solution.md).
