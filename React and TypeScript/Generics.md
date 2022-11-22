We've gotten this far without really getting in the weeds over some of TypeScript's more confusing syntax. Up until this point, I have handwaved over the syntax for generic type variables, but we're going to be using them a bit more in the next section of this course. It makes sense for us to take a moment or two to *really* understand what's going on here.

**Nota bene**: We're going to learn *just enough* about generics to make our lives easier as we implement some common component patterns in React. It's *not* our goal to have an exhaustive discussion on the topic. If you're interested in learning more, I recommend checking out the workshops on [TypeScript Fundamentals](https://frontendmasters.com/courses/typescript-v2/) and [Production-Grade TypeScript](https://frontendmasters.com/courses/production-typescript/).

[Generics](https://www.typescriptlang.org/docs/handbook/2/generics.html) allow us to be a little bit more flexible with our type system. You can think of them as variables for your types.

You might have seen some syntax at looks like this.

```tsx
type Link<T> = {
  value: T;
  next: Link<T>;
};
```

We can't just make a new object with that type.

```ts
const link: Link = {};
```

Now, we can define what type `T` should be.

```ts
const link: Link<string> = { value: 'hello' };
```

It's kind of like a variable.

You can use this variable to pay it forward a bit.

```ts
const firstLink: Link<number> = {
  value: 2,
  next: {
    value: 'string', // This won't work.
  },
};
```

This will work, however:

```ts
const firstLink: Link<number> = {
  value: 2,
  next: {
    value: 4,
  },
};
```

TypeScript will try to help you out as much as possible.

You can also use this in a function.

```ts
function identity<T>(arg: T) {
  return arg;
}
```

This would be not great if we had to make new functions for every different type that we anted to use the indentity function on.

```ts
identity<number>(3);
```

It turns out that TypeScript will try its darnest to help out.

```ts
identity(3);
```

I'm just going to casually mention that this might be important for our understanding later.

## Use With Arrow Functions

You need a weird comma if JSX is enabled otherwise the compile gets confused as to whether or not you're writing the opening tag for JSX or a generic.

```ts
const createNode = <T>(value: T): Link<T> => ({ value });

const addNext = <T>(node: Link<T>, value: T): Link<T> => {
  node.next = createNode(value);
  return node;
};

const createNodeAndNext = <T>(first: T, second: T) => {
  const firstNode = createNode(first);
  firstNode.next = createNode(second);
  return firstNode;
};

const createNodeAndNextTapped = <T>(first: T, second: T): Link<T> =>
  tap(createNode(first), (node) => addNext(node, second));

const node = createNode(4);
const nextNode = addNext(node, 5);
const twoNodes = createNodeAndNextTapped(1, 2);
const twoMoreNodes = createNodeAndNext(4, 5);
```
