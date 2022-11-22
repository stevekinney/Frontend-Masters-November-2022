Let's take a moment to look at some of the types that go along with some of the more common props that we tend to see in React applications.

## Primitives

For starters, we have our basic primitives.

```ts
type CounterProps = {
  incident: string;
  count: number;
  enabled: boolean;
};
```

## Arrays

We can also have arrays or collections of primitives.

```ts
type GroceryListProps = {
  items: string[];
};
```

## Unions and intersections

Sometimes, we don't want to allow *any* string—only certain strings. We can use a [union type](https://www.typescriptlang.org/docs/handbook/unions-and-intersections.html) to represent this.

```ts
type GroceryListProps = {
  items: string[];
  status: 'loading' | 'error' | 'success';
};
```

**Let the record show**: This is one of my favorite things about using TypeScript in React. I don't want to talk about how many hours of my life I've lossed due to a silly misspelling.

## Objects

It's not uncommon for us to find ourselves using objects in JavaScript (erm, TypeScript). So, what would that look like?

```ts
type ContrivedExampleComponmentProps = {
  anObject: object; // Useful as a placeholder.
  anotherObject: {}; // Can have any properties and values.
  yetAnotherObject: Record<string, string>;
  // 👆 Takes an object where the keys are strings and so are the values.
  item: {
    id: string;
    title: string;
  };
  items: {
    id: string;
    title: string;
  }[]; // An array of objects of a certain shape.
};
```

We could refactor this a bit. (I know, it's a contrived example, but go along with it.)

```ts
type Item = {
  id: string;
  title: string;
};

type ContrivedExampleComponmentProps = {
  item: Item;
  items: Item[];
};
```

So, if you look at our two object examples above, we're missing something.

- `{}` will allow for an object with any keys and any values.
- `{ id: string; title: string; }` will only allow for an object with *two* keys: `id` and `title` as long as those values are both strings.

But, what if we wanted to find a happy medium? What if we wanted a situation where we said, "Listen, the key can be _any_string and the value has to be of a certain type.

That might look something like this:

```ts
type ItemHash = {
  [key: string]: Item;
};
```

Or, if we wanted to say the keys are number and the values are strings, it would look like this:

```ts
type Dictionary = {
  [key: number]: string;
};
```

Another way of writing either of those would be as follows:

```ts
Record<string, Item>;
```

## Functions

Okay, so we tend to also pass functions around, right? What does that look like?

```ts
type ContrivedExampleProps = {
  // Does not take any arguments. Does not return anything.
  onHover: () => void;
  // Takes a number. Returns nothing (e.g. undefined).
  onChange: (id: number) => void;
  // Takes an event that is based on clicking on a button.
  // Returns nothing.
  onClick(event: React.MouseEvent<HTMLButtonElement>): void;
};
```

A standalone function that you type as your declare it, is a little bit different.

```ts
const add = (a: number, b: number): number => {
  return a + b;
};

function subtract(a: number, b: number): number {
  return a - b;
}
```

But, you can separate the type from the function definition if you really insist.

```ts
type Multiply = (a: number, b: number) => number;

const multiply: Multiply = (a, b) => a + b;
```

## Optional properties

We covered this [previously](Refactoring%20from%20PropTypes,%20a%20solution.md), but let's add it here for the sake of making this reference complete. Adding `?` signifies that you're cool with this being `undefined`.

```ts
type ContrivedProps = {
  requiredProp: boolean;
  optionalProp?: string;
};
```
