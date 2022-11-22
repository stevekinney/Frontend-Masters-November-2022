React includes a bunch of utility types for common events.

- `ClipboardEventHandler`
- `CompositionEventHandler`
- `DragEventHandler`
- `FocusEventHandler`
- `FormEventHandler`
- `ChangeEventHandler`
- `KeyboardEventHandler`
- `MouseEventHandler`
- `TouchEventHandler`
- `PointerEventHandler`
- `UIEventHandler`
- `WheelEventHandler`
- `AnimationEventHandler`
- `TransitionEventHandler`

You can import these types when you're building a component where you want to accept a prop that has the same signature as what React would expect. This is super useful when you're passing a prop down to an HTML element and don't have time to play games trying to writing a custom type that appeases what React is looking for.

```tsx
import { MouseEventHandler } from 'react';

type ButtonProps = {
  label: string;
  variant: 'primary' | 'secondary' | 'danger';
  onClick: MouseEventHandler;
};

const Button = ({ label, variant, onClick }: ButtonProps) => {
  return (
    <button className={variant} onClick={onClick}>
      {label}
    </button>
  );
};
```
