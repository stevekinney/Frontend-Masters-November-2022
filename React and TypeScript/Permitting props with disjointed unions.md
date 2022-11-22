([Source](https://onesignal.com/blog/effective-typescript-for-react-applications/))

## Example One

Start out with a button component that is basically supposed to be like a regular button but with a label (e.g. *primary*, *secondary*, *danger*, etc.). Try adding all of the things we might need from a button one at a time.

Show how we cant extend the props of a button using `React.ComponentPropsWithoutRef<"button">` and then spread those onto the button. Now we have a _super_ button and we're not reinventing the wheel.

## Example Two

Show how we might to do something like this:

```tsx
type ButtonKind = "primary" | "secondary";

// Build separate interfaces for Primary & Secondary buttons
interface PrimaryButton {
  kind: "primary";
  specialPrimaryMethod: () => void;
}

interface SecondaryButton {
  kind: "secondary";
}

// Create a disjoint union
type Button = PrimaryButton | SecondaryButton;

// Add built-in HTML props to the disjoin union
type Props = React.ComponentPropsWithoutRef<"button"> & Button;

// You can no longer destructure props since specialPrimaryMethod
// doesn't always exist on the object.
function Button(props: Props) {
  const handleClick = (e: React.MouseEvent<HTMLButtonElement>) => {
    if (props.kind === "primary") {
      // No extra if check needed!
      props.specialPrimaryMethod();
    } else {
      props.onClick?.(e);
    }
  };

  return <button onClick={handleClick}>{props.children}</button>;
}
```

