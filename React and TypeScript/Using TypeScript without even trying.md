---
repository: https://github.com/stevekinney/name-badges
branch: main
---

**Alternative title**: Using TypeScript with Intellisense.

We're going to start off in [this repository](https://github.com/stevekinney/name-badges) for the time being.

Alright, let's start simple here and play a fun game: Is the component below written in JavaScript or TypeScript?

```tsx
const NameBadge = () => {
  return (
    <section className="badge">
      <header className="badge-header">
        <h1 className="text-5xl">HELLO</h1>
        <p>My name is…</p>
      </header>
      <div className="badge-body">
        <p className="badge-name">Steve</p>
      </div>
      <footer className="badge-footer" />
    </section>
  );
};

export default NameBadge;
```

The correct answer is "yes." If we're being pedantic, since the file ends in `.tsx`, it's _technically_ TypeScript, but if I changed the file extension to `.jsx`, it would be perfectly fine. (Franky, other than all the `className` attributes, it could just be HTML with a little tweaking. There isn't really much going on with this component.)

This is one of the core principles that I want us to remember during our time together: Generally speaking, **TypeScript tries its best to get our of your way**. It will do it's best to figure out what's going on. You only need to step in when it can't figure it out on your behalf.

But, you will notice that Visual Studio Code's Intellisense is trying its best to figure out the types for you. If you hover over `NameBadge`, you'll see the following:

```ts
const NameBadge: () => JSX.Element;
```

This makes sense. Functions are valid React components these days and since we're returning JSX, it deduced that the return type of this function is a `JSX.element`.

For fun, let's add a simple little function at the bottom of the file just to confirm our suspicions.

```ts
const four = () => 2 + 2;
```

If you hover this function, you'll see:

```ts
const four: () => number;
```

We didn't have to give the function a return type. TypeScript figured it out. It analyzed the function and saw that there is only one kind of value that is ever coming out of the function—a number. So, it went ahead and typed it for us.

If we add some chaos to the equation, you'll see that TypeScript respects the confusion that you caused.

```diff
 const NameBadge = () => {
+  if (Math.round(Math.random())) return null;
   return (
```

Now, TypeScript tells us that this function will give us either a `JSX.Element` _or_ it's going to give us `null` depending on which way the winds blow.

```ts
const NameBadge: () => JSX.Element | null;
```

Alright. Enough of that. I think I made my point. Let's learn how to [migrate from PropTypes](Migrating%20from%20PropTypes.md).
