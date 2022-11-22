## `NewItem`

```diff
@@ -1,6 +1,7 @@
 import { memo, useState } from 'react';
+import { add } from '../lib/reducer';

-const NewItem = ({ addItem }) => {
+const NewItem = ({ dispatch }) => {
   const [newItemName, setNewItemName] = useState('');

   return (
@@ -8,7 +9,7 @@ const NewItem = ({ addItem }) => {
       id="new-item"
       onSubmit={(e) => {
         e.preventDefault();
-        addItem(newItemName);
+        dispatch(add(newItemName));
         setNewItemName('');
       }}
     >

```

## `MarkAllAsPacked`

```diff
@@ -1,6 +1,8 @@
-const MarkAllAsUnpacked = ({ onClick }) => (
+import { markAllAsUnpacked } from '../lib/reducer';
+
+const MarkAllAsUnpacked = ({ dispatch }) => (
   <div className="mb-16">
-    <button className="w-full" onClick={onClick}>
+    <button className="w-full" onClick={dispatch(markAllAsUnpacked())}>
       🏠 Mark All As Unpacked
     </button>
   </div>
```
