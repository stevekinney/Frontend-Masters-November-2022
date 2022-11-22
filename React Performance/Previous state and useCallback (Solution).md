Our new state actions will now look something like this:

```ts
const add = useCallback(
  (name) => {
    const item = createItem(name);
    setItems((previousItems) => [...previousItems, item]);
  },
  [setItems],
);

const update = useCallback(
  (id, updates) => {
    setItems((items) => updateItem(items, id, updates));
  },
  [setItems],
);

const remove = useCallback(
  (id) => {
    setItems((items) => removeItem(items, id));
  },
  [setItems],
);

const markAllAsUnpacked = useCallback(() => {
  return setItems((items) => items.map((item) => ({ ...item, packed: false })));
}, [setItems]);
```

Take a look at that. The `Application` itself re-renders and so do the two lists that—yea—are new arrays. But the individual items don't change when a new item is added or some other item is changed and `NewItem` doesn't re-render when an item is toggled.

![](<_attachments/Previous%20State%20(Complete).gif>)

## What changed?

```diff
--- a/src/components/application.js
+++ b/src/components/application.js
@@ -22,20 +22,28 @@ const Application = () => {
     [setItems],
   );

-  const update = (id, updates) => {
-    setItems(updateItem(items, id, updates));
-  };
+  const update = useCallback(
+    (id, updates) => {
+      setItems((items) => updateItem(items, id, updates));
+    },
+    [setItems],
+  );

-  const remove = (id) => {
-    setItems(removeItem(items, id));
-  };
+  const remove = useCallback(
+    (id) => {
+      setItems((items) => removeItem(items, id));
+    },
+    [setItems],
+  );

   const unpackedItems = filterItems(items, { packed: false });
   const packedItems = filterItems(items, { packed: true });

-  const markAllAsUnpacked = () => {
-    return setItems(items.map((item) => ({ ...item, packed: false })));
-  };
+  const markAllAsUnpacked = useCallback(() => {
+    return setItems((items) =>
+      items.map((item) => ({ ...item, packed: false })),
+    );
+  }, [setItems]);

   return (
     <main className="flex flex-col gap-8 p-8 mx-auto lg:max-w-4xl">
```
