---
repository: https://github.com/stevekinney/colors
branch: typing-actions-exercise
endBranch: typing-actions-solution
---

Alright, let's get one of those `ColorChangeSwatches` wired up to our state management. We've already got the action in place from earlier, so that's nice.

Let's go for those saved colors that don't really save. (They're hard-coded.)

we're get ready for our `dispatch` prop in `src/components/saved-colors/index.tsx`:

In `src/components/application.tsx`:

```diff
@@ -22,7 +22,7 @@ const Application = () => {
       />
       <AdjustColors hexColor={hexColor} dispatch={dispatch} />
       <RelatedColors hexColor={hexColor} />
-      <SavedColors hexColor={hexColor} />
+      <SavedColors hexColor={hexColor} dispatch={dispatch} />
     </div>
   );
 };
```

```diff
@@ -1,10 +1,11 @@
-import { useState } from 'react';
+import { Dispatch, useState } from 'react';
 import id from 'lodash.uniqueid';
 import AddSavedColor from './add-saved-color';
 import SavedColor from './saved-color';

 type SavedColorsProps = {
   hexColor: string;
+  dispatch: Dispatch<ColorActions>;
 };

 const saved = [
@@ -12,7 +13,7 @@ const saved = [
   { id: id(), name: 'Blue Fire', hexColor: '#00aadd' },
 ];

-const SavedColors = ({ hexColor }: SavedColorsProps) => {
+const SavedColors = ({ hexColor, dispatch }: SavedColorsProps) => {
   const [savedColors, setSavedColors] = useState(saved);

   return (
@@ -29,6 +30,9 @@ const SavedColors = ({ hexColor }: SavedColorsProps) => {
             key={id}
             name={name}
             hexColor={hexColor}
+            onClick={() =>
+              dispatch({ type: 'update-hex-color', payload: { hexColor } })
+            }
             onRemove={() =>
               setSavedColors((colors) =>
                 colors.filter((color) => color.id !== id),
```

A branch with the solution is waiting for you [here](https://github.com/stevekinney/colors/tree/typing-actions-solution).

That being said, I'm tired of passing dispatch through. We've made our point. Let's go use [the Context API](The%20Context%20API.md).
