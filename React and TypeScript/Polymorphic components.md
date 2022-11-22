---
repository: https://github.com/stevekinney/colors
branch: mirroring-solution
endBranch: polymorphic
---

Let's walk along the razors edge between practicalk and academic. I'm including this section because once or twice, I've needed to use the techiques we're about to cover, but those occasions were few and far between.

When I worked at Twilio, they had a team dedicated to building out their design system, [Paste](https://paste.twilio.design). The foundational component was [`Box`](https://paste.twilio.design/primitives/box). Everything was build on top of `Box`.

Did you need to build something custom that wasn't in Paste? Fine, but [start with `Box`](https://paste.twilio.design/primitives/box/#when-to-use-box).

Here is an example from their [documentation](https://paste.twilio.design/primitives/box) on `Box`.

```tsx
<Box as="article" backgroundColor="colorBackgroundBody" padding="space60">
  Parent box on the hill side
  <Box
    backgroundColor="colorBackgroundSuccessWeakest"
    display="inline-block"
    padding="space40"
  >
    nested box 1 made out of ticky tacky
  </Box>
</Box>
```

Earlier, I told you that over the course of building out a large application, I tend to end up making a few wrappers around components. `Box` is a primitive wrapper around the basic block elements in HTML (e.g. `div`, `aside`, `section`, `article`, `main`, `head`, etc.). But, like we don't want to lose all of our semantic meaning that we get from all of these tags, but we also don't need like 20 variation on `Box` that are all basically the same. What we'd like to is use `Box` but also be able to specify what it ought to be under the hood.

Here is an overly-simplified take on a `Box` element inspired by [Styled Components](https://medium.com/styled-components/announcing-styled-components-v4-better-faster-stronger-3fe1aba1a112).

```tsx
import { PropsWithChildren } from 'react';

type BoxProps = PropsWithChildren<{
  as: 'div' | 'section' | 'article' | 'p';
}>;

const Box = ({ as, children }: BoxProps) => {
  const TagName = as || 'div';
  return <TagName>{children}</TagName>;
};

export default Box;
```

We refine `as` to something JSX will respect as a component type and it all works out.

Well, not really. I can't exactly pass any props to it. I could get away with something like this:

```tsx
type BoxProps = ComponentPropsWithRef<'div'> & {
  as: 'div' | 'section' | 'article' | 'p';
};

const Box = ({ as, children }: BoxProps) => {
  const TagName = as || 'div';
  return <TagName>{children}</TagName>;
};
```

I honestly don't even know if like `section`s have any properties that `div` doesn't and while I am sure I could look it up, none of us feel good about this implementation.

But, like what's that `'div'` being passed in there and how does it work? If we look at the type definition for `ComponentPropsWithRef`, we see the following:

```tsx
type ComponentPropsWithRef<T extends ElementType> = T extends new (
  props: infer P,
) => Component<any, any>
  ? PropsWithoutRef<P> & RefAttributes<InstanceType<T>>
  : PropsWithRef<ComponentProps<T>>;
```

Nevermind all of those ternaries. I am interested in `ElementType` right now.

```tsx
type BoxProps = ComponentPropsWithRef<'div'> & {
  as: ElementType;
};
```

![](_attachments/Pasted%20image%2020221114204641.png)

Okay, that's interesting, but what if I wanted the type argument I give to `ComponentPropsWithRef` to be the same as… `as`?

We _could_ try something like this…

```tsx
type BoxProps<E extends ElementType> = Omit<
  ComponentPropsWithoutRef<E>,
  'as'
> & { as?: E };
```

And we _should_ now see that we have type that adapts to whatever it is set to.

## Extension

We can expand on this to do something like this:

```tsx
import { ComponentPropsWithoutRef, PropsWithChildren } from 'react';

type ButtonType = 'a' | 'button';

type ButtonProps<T extends ButtonType, E = ComponentPropsWithoutRef<T>> = {
  as?: T;
} & PropsWithChildren<E>;

const Button = <T extends ButtonType = 'button'>({
  as,
  ...props
}: ButtonProps<T>): JSX.Element => {
  if (as === 'a') {
    return <a {...props}>{props.children}</a>;
  }
  return <button {...props}>{props.children}</button>;
};

const Example = () => {
  return (
    <div>
      <Button type="submit">Hello</Button>
      <Button href="http://wow.com">Link</Button>
    </div>
  );
};

export default Example;
```

## Further Reading

There is *a lot more* that we could discuss on this topic, but that's probably not only a workshop in and of itself but one that is more of academic exercise than something you're going to practically need to use in your day-to-day endeavors—unless you're building a highly generalized library or working something like React's types directly. Out of curiosity, I took a look at Paste's Box implementation and they just did the work of listing out all of the props directly and I can't argue that wasn't the right choice. I find myself [mirroring HTML components](Mirroring%20HTML%20components.md) existing components somewhat regularly. Polymorphic components? Not so much.

If you're interested in seeing how deep this rabbit hole can go. Here is some light reading:

- [Writing Type-Safe Polymorphic React Components (Without Crashing TypeScript)](https://blog.andrewbran.ch/polymorphic-react-components/)
- [Build strongly typed polymorphic components with React and TypeScript ](https://blog.logrocket.com/build-strongly-typed-polymorphic-components-react-typescript/)
- [kripod/react-polymorphic-box: Building blocks for strongly typed polymorphic components in React. (github.com)](https://github.com/kripod/react-polymorphic-box)
