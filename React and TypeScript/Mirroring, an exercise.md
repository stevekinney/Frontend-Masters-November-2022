---
repository: https://github.com/stevekinney/colors
branch: mirroring
endBranch: mirroring-solution
---

In `src/components/shared/button.tsx`, I have another candidate for passing through some properties.

```tsx
type ButtonProps = {
  variant?: string;
  size?: string;
  className?: string;
  style?: CSSProperties;
  onClick?: MouseEventHandler<HTMLButtonElement>;
};
```

The only things that are particularly special are the `variant` and `size` properties.

## Extension

You'll notice that I've never taught a workshop on CSS or design—and that's for a good reason. But, if you look at my CSS, then you'll see that really there are only three variants: `normal`, `primary`, and `destructive`. And, there are really only two sizes: `normal` and `small`. The two normal ones aren't even really variants or sizes—they're the absense of those values.

And yet, I'm just letting like any ol' string through? Could we tighten that up a bit?

When you're ready, we'll move on to [[Mirroring, a solution|a solution]].