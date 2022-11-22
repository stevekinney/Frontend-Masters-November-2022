---
repository: https://github.com/stevekinney/colors
branch: create-context
endBranch: mirroring
---

Okay, let's take a look at something that's been bothering me for a bit. As I assume that I have mentioned at this point in the workshop, I have some issues with some of the defaults for a lot of the DOM. And, like thse have mostly been example applications. Usually, there are a lot of abstractions that I end up making for myself in larger applications.

But—as much as I might complain—there is a lot of functionality-baked into these time-tested elements and I'd still like to take advantage of that. Now, in JavaScript land, I can just use something like `{...props}` to pass through my props to an underlying HTML element. The problem is that TypeScript isn't really aware of what I'm doing—_yet_.

Let's take a look at my labeled input component in my color palette application. It's hanging out in `src/components/shared/labeled-input.tsx` for those of you keeping track at home.

We only need to look at the type to see what my issue is.

```tsx
type LabeledInputProps = {
  id?: string;
  label: string;
  value: string | number;
  type?: string;
  className?: string;
  onChange?: ChangeEventHandler<HTMLInputElement>;
};
```

Other than `label`, which I'm passing to the—umm—label that I often want grouped with my inputs, I'm just manually passing props through one by one. And it's tedious. Do I want to add `autofocus`? Better add another prop. I'd much prefer to do something like this:

```tsx
const LabeledInput = ({ label, ...props }: LabeledInputProps) => {
  const id = useId() + props.id;

  return (
    <div>
      <label htmlFor={id}>{label}</label>
      <input
        {...props}
        id={id}
        readOnly={!props.onChange}
        className={clsx('w-full', props.className)}
      />
    </div>
  );
};
```

I'll pull out the things that I need to work with and then just pass everything else on through to the `<input />` component and then just pretend for the rest of my days that it's a regular ol' `HTMLInputElement` that and live through the rest of my days.

And out of the box, TypeScript doesn't really care since `HTMLElement` is pretty flexible. The DOM does pre-date this whole TypeScript thing, you know. It only complains if I toss something completely egregious in there.

But, I can't _use_ any of those properties.

```tsx
<LabeledInput label="H" type="number" value={h} readOnly />
```

Even in a best case scenario, I don't get any auto-complete or anything. What I want is for TypeScript to give me all of the goodness that I would get with a normal input.

## Combining with the types of an HTML element

So, TypeScript wants me to be explicit. Fine, I'll be explicit about the fact that I want my custom stuff _plus_ everything that a particular HTML element has.

```ts
type LabeledInputProps = ComponentPropsWithoutRef<'input'> & {
  label: string;
};
```

![](_attachments/Pasted%20image%2020221114194739.png)

Now, it all works _and_ I get auto complete. As far as TypeScript is concerned, this is _basically_ an `<input />` field, but it automatically wires up the label as appropriate.

We can also look at how it might work on [[Mirroring, an exercise|a button]].
