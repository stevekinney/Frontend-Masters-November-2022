---
repository: https://github.com/stevekinney/colors
branch: main
endRepository: typing-actions-exercise
---

Alright, let's start with a new application that allows us to [pick and save colors](https://github.com/stevekinney/colors). I'll start us off and then you'll take it from there for a bit.

Right now there is a fair bit of prop drilling and not all of the functionality works just yet. You can select a new color from the color input, but you can't adjust it from anywhere else.

My sense is that this would be easier with a reducer and maybe eventually [the Context API](Using%20state%20management%20and%20the%20Context%20API).

Let's start by refactoring the application to use a reducer. I'm going to put mine in `lib/reducer.ts`, but you can do whatever feels best for you at this time in your life.

## Starting with some actions

I know at least two of the actions that I'm going to want, so I am going to go ahead and make them now.

```ts
type UpdateHexColorAction = {
  type: 'update-hex-color';
  payload: {
    hexColor: string;
  };
};

type UpdateRGBColorAction = {
  type: 'update-rgb-color';
  payload: { rgb: [r: number, g: number, b: number] };
};
```

My state is also pretty simple. I could—and maybe _should_ add the list of saved colors, but for now let's start simple as pretty much everything revolves around the `hexColor` that I have in the top level of my applicastion state.

```ts
type ColorState = {
  hexColor: string;
};

export const initialState: ColorState = {
  hexColor: '#ff7799',
};
```

And with that, I think I am ready to make my reducer.

```ts
const colorReducer = (
  state: ColorState,
  action: UpdateHexColorAction | UpdateRGBColorAction,
) => {
  if (action.type === 'update-hex-color') {
    const { hexColor } = action.payload;
    return { ...state, hexColor };
  }

  if (action.type === 'update-rgb-color') {
    const hexColor = rgb.hex(action.payload.rgb);
    return { ...state, hexColor };
  }

  return state;
};

export default colorReducer;
```

## Hooking up the reducer

Alright, we're ready to hook this thing up.

Let's switch from `useState` to `useReducer` in `src/components/application.tsx`.

```diff
@@ -1,17 +1,24 @@
-import { useState } from 'react';
+import { useReducer } from 'react';
 import SavedColors from './saved-colors';
 import RelatedColors from './related-colors';
 import AdjustColors from './adjust-colors';
 import ColorPicker from './color-picker';
+import colorReducer, { initialState } from '../lib/color-reducer';

 const Application = () => {
-  const [hexColor, setHexColor] = useState('#e56e24');
+  const [state, dispatch] = useReducer(colorReducer, initialState);
+  const hexColor = state.hexColor;

   return (
     <div className="grid max-w-3xl grid-cols-1 gap-8 p-8 pb-40 mx-auto dark:bg-slate-900 dark:text-white sm:grid-cols-2">
       <ColorPicker
         hexColor={hexColor}
-        onChange={(e) => setHexColor(e.target.value)}
+        onChange={(e) =>
+          dispatch({
+            type: 'update-hex-color',
+            payload: { hexColor: e.target.value },
+          })
+        }
       />
       <AdjustColors hexColor={hexColor} />
       <RelatedColors hexColor={hexColor} />

```

We can verify that everything works as expected.

## Adjust the color by the RGB value

This is where we're going to hit another areas that we'll want to circle back and improve. But, let's try to figure it out first.

We can see that `dispatch` has the following type:

```ts
const dispatch: React.Dispatch<UpdateHexColorAction | UpdateRGBColorAction>;
```

That's a union of all of our actions. Let's actually store that type somewhere so that we can get access to it.

Let's create `lib/color-state.d.ts`, and we'll move our types in there.

```ts
type UpdateHexColorAction = {
  type: 'update-hex-color';
  payload: {
    hexColor: string;
  };
};

type UpdateRGBColorAction = {
  type: 'update-rgb-color';
  payload: { rgb: [r: number, g: number, b: number] };
};

type ColorState = {
  hexColor: string;
};

type ColorActions = UpdateHexColorAction | UpdateRGBColorAction;
```

We'll update our reducer to use that new `ColorActions` type we just made.

```ts
const colorReducer = (
  state: ColorState = initialState,
  action: ColorActions,
) => {
  // …
};

export default colorReducer;
```

Now, if we look at `dispatch`, it's a lot easier to wrap our heads around:

```ts
const dispatch: React.Dispatch<ColorActions>;
```

## Typing and passing `dispatch` through as a prop

We can now reference `dispatch`'s type super easily. Let's look at `src/components/adjust-colors/index.ts`:

```tsx
type AdjustColorsProps = {
  hexColor: string;
  dispatch: Dispatch<ColorActions>;
};
```

I'll go ahead and do the same for `HexToRGB`.

```tsx
type HexToRGBProps = {
  hexColor: string;
  dispatch: Dispatch<ColorActions>;
};
```

There is definitely an opportunity to make some of these types more reusable, but we'll put that off for a bit. Let's thread `dispatch` from `Application` through `AdjustColor` to `HexToRGB`.

In `src/components/application.tsx`:

```diff
@@ -20,7 +20,7 @@ const Application = () => {
           })
         }
       />
-      <AdjustColors hexColor={hexColor} />
+      <AdjustColors hexColor={hexColor} dispatch={dispatch} />
       <RelatedColors hexColor={hexColor} />
       <SavedColors hexColor={hexColor} />
     </div>
```

In `src/components/adjust-colors/`:

```diff
@@ -10,11 +10,11 @@ type AdjustColorsProps = {
   dispatch: Dispatch<ColorActions>;
 };

-const AdjustColors = ({ hexColor }: AdjustColorsProps) => {
+const AdjustColors = ({ hexColor, dispatch }: AdjustColorsProps) => {
   return (
     <div className="flex flex-col w-full gap-4">
       <h3>Adjust Colors</h3>
-      <HexToRGB hexColor={hexColor} />
+      <HexToRGB hexColor={hexColor} dispatch={dispatch} />
       <HexToHSL hexColor={hexColor} />
       <HexToHSV hexColor={hexColor} />
       <HexToCMYK hexColor={hexColor} />
```

And finally, in `src/components/adjust-colors/to-rgb.tsx`:

```diff
@@ -7,7 +7,7 @@ type HexToRGBProps = {
   dispatch: Dispatch<ColorActions>;
 };

-const HexToRGB = ({ hexColor }: HexToRGBProps) => {
+const HexToRGB = ({ hexColor, dispatch }: HexToRGBProps) => {
   const color = hex.cmyk(hexColor);
   const [r, g, b] = color;
```

## Firing an action when the RGB values change

We've got some more optimization to do, but we'll revisit this later. Our mission right now is to dispatch that action with the newest RGB values.

```ts
const HexToRGB = ({ hexColor, dispatch }: HexToRGBProps) => {
  const color = hex.rgb(hexColor);
  const [r, g, b] = color;

  const updateRGB = ({ red = r, green = g, blue = b }) => {
    dispatch({
      type: 'update-rgb-color',
      payload: { rgb: [red, green, blue] },
    });
  };

  return (
    <section className="grid w-full grid-flow-col gap-2">
      <LabeledInput
        label="R"
        type="number"
        value={r}
        onChange={(e) => updateRGB({ red: +e.target.value })}
      />
      <LabeledInput
        label="G"
        type="number"
        value={g}
        onChange={(e) => updateRGB({ green: +e.target.value })}
      />
      <LabeledInput
        label="B"
        type="number"
        value={b}
        onChange={(e) => updateRGB({ blue: +e.target.value })}
      />
    </section>
  );
};

export default HexToRGB;
```
