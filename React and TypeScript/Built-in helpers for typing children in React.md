- `React.PropsWithChildren`
- `React.ComponentPropsWithoutRef`
- `FunctionalComponent<Props>` and React 18
- `React.HTMLProps`

Let's say you want to extend props for a given React component to take children. You can use `React.PropsWithChildren`.

This might look something like this:

```tsx
type ButtonProps = React.PropsWithChildren<{
  onClick: () => void;
}>;

const Button = ({ children, onClick }: ButtonProps) => {
  return <button onClick={onClick}>{children}</button>;
};
```

You also have `React.ComponentPropsWithoutRef`, which you could—and we will—use as follows:

```tsx
type ButtonProps = React.ComponentPropsWithoutRef<'button'>;

const Button = ({ children, onClick, type }: ButtonProps) => {
  return (
    <button onClick={onClick} type={type}>
      {children}
    </button>
  );
};
```

Now, `Button` has all of the some props as the native `<button>` element from the DOM.

🚨 **Important note**: Prior to React 18, you coulkd also use a [`FunctionalComponent<Props>`](https://www.newline.co/@bespoyasov/how-to-define-props-with-children-in-react-typescript-app--56bd18be#using-functioncomponent-or-fc) or `Component<Props>` to accomplish a similar goal:

```tsx
import * as React from 'react';

// 🚨 No good anymore.
const Input: React.FC = ({ children }) => <div>{children}</div>;
```

But, this was removed when [the types were refactored for React 18](https://github.com/DefinitelyTyped/DefinitelyTyped/pull/56210). You can read more about the rationale [here](https://solverfox.dev/writing/no-implicit-children/).
