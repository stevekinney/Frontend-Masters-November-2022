---
repository: https://github.com/stevekinney/colors
branch: context-api
endBranch: context-hooks
---

## Creating a `useDispatch` Hook

For starters, we can avoid needed to bring in both the `ColorContext` and `useContext` hook by creating a `useDispatch` hook that brings them both together in an easily digestible format.

```ts
export const useDispatch = () => {
  const { dispatch } = useContext(ColorContext);
  return useMemo(() => dispatch, [dispatch]);
};
```

And now, we can use that anywhere we might have needed `dispatch` out of `createContext`.

For example, we can use it in `src/components/shared/color-change-swatch.tsx`:

```tsx
const ColorChangeSwatch = ({ hexColor, className }: ColorChangeSwatchProps) => {
  const dispatch = useDispatch();

  return (
    // …
  );
};
```

If we check out [this branch](#), I've taken the libery of doing two things: filling out the rest of our actions in the reducer. I also created a number of custom hooks that typed for us out of the box.

I'll include them here for convenience.

First of all, we've got out actions:

```ts
const colorReducer = (
  state: ColorState = initialState,
  action: ColorActions,
) => {
  if (action.type === 'update-hex-color') {
    const { hexColor } = action.payload;
    return { ...state, hexColor };
  }

  if (action.type === 'update-rgb-color') {
    const hexColor = '#' + rgb.hex(action.payload.rgb);
    return { ...state, hexColor };
  }

  if (action.type === 'update-hsl-color') {
    const hexColor = '#' + hsl.hex(action.payload.hsl);
    return { ...state, hexColor };
  }

  if (action.type === 'update-hsv-color') {
    const hexColor = '#' + hsv.hex(action.payload.hsv);
    return { ...state, hexColor };
  }

  if (action.type === 'update-cmyk-color') {
    const hexColor = '#' + cmyk.hex(action.payload.cmyk);
    return { ...state, hexColor };
  }

  return state;
};
```

Next up, let's look at `src/hooks.ts`:

```ts
import { useCallback, useContext, useMemo } from 'react';
import { ColorContext } from './context';

export const useDispatch = () => {
  const { dispatch } = useContext(ColorContext);
  return useMemo(() => dispatch, [dispatch]);
};

export const useHexColor = () => {
  const { state } = useContext(ColorContext);
  return useMemo(() => state.hexColor, [state]);
};

export const useUpdateHexCode = () => {
  const dispatch = useDispatch();
  return useCallback(
    (hexColor: string) =>
      dispatch({ type: 'update-hex-color', payload: { hexColor } }),
    [dispatch],
  );
};

export const useUpdateRGB = () => {
  const dispatch = useDispatch();
  return useCallback(
    (rgb: RGB) => dispatch({ type: 'update-rgb-color', payload: { rgb } }),
    [dispatch],
  );
};

export const useUpdateHSL = () => {
  const dispatch = useDispatch();
  return useCallback(
    (hsl: HSL) => dispatch({ type: 'update-hsl-color', payload: { hsl } }),
    [dispatch],
  );
};

export const useUpdateHSV = () => {
  const dispatch = useDispatch();
  return useCallback(
    (hsv: HSV) => dispatch({ type: 'update-hsv-color', payload: { hsv } }),
    [dispatch],
  );
};

export const useUpdateCMYK = () => {
  const dispatch = useDispatch();
  return useCallback(
    (cmyk: CMYK) => dispatch({ type: 'update-cmyk-color', payload: { cmyk } }),
    [dispatch],
  );
};
```

