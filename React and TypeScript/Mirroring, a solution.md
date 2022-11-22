Alright, let's see if we can slim this thing down a bit. As I mentioned, only `variant` and `size` are particularly special.

```tsx
type ButtonProps = ComponentPropsWithoutRef<'button'> & {
  variant?: string;
  size?: string;
};
```

Moving down to the component itself, even `PropsWithChildren` is a bit repetitive because guess what buttons normally have? That's right, they have children. So, boom. That can go too.

```tsx
const Button = ({ variant, size, className, ...props }: ButtonProps) => {
  return <button {...props} className={clsx(variant, size, className)} />;
};
```

Check it out. I don't even have to explicity put the `{children}` in there either. It's one of the props, so it's passed to the button.

## Using intersections for the variant and size

We can do some additional tweaks order to make things even better.

```tsx
type ButtonProps = ComponentPropsWithoutRef<'button'> & {
  variant?: 'primary' | 'secondary' | 'destructive';
  size?: 'small' | 'normal' | 'large';
};

const Button = ({
  variant = 'secondary',
  size = 'normal',
  className,
  ...props
}: ButtonProps) => {
  return <button {...props} className={clsx(variant, size, className)} />;
};
```

And now, I get more autocomplete!

![](_attachments/Pasted%20image%2020221114201058.png)

Honestly? That's not a lot of work for such a nice benefit. I don't want to talk about how many times I've mispelled the word "success" and then not understood why my code wasn't working as expected.
