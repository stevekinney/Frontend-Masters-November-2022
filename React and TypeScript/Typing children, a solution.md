---
repository: https://github.com/stevekinney/typing-children
branch: solution
---

How do we type [this](Typing%20children,%20an%20exercise.md)? Well. We have a few choices.

- `JSX.Element;`: 💩 This doesn't account for arrays (e.g. multiple elements) at all.
- `JSX.Element | JSX.Element[];` 😕 This doesn't accept strings.
- `React.ReactChildren;`: 🤪 Not at even an appropriate type; it's a [utility function](https://reactjs.org/docs/react-api.html#reactchildren).
- `React.ReactChild[];`: 😀 Accepts multiple children, but it doesn't accept a single child.
- `React.ReactNode;`: 🏆 Accepts everything.

This will get everything working as expected:

```ts
import React, { ReactNode } from 'react';

type BoxProps = { children: ReactNode };
```

## A helpful utility type

This is a pretty simple utility type that you _could_ write yourself, but you don't have to.

```ts
type PropsWithChildren<P = unknown> = P & {
  children?: ReactNode | undefined;
};
```

It's basically an easy way to say, "This component can render children." That's it. This means, we can refactor our example a little more.

```tsx
import React, { PropsWithChildren } from 'react';

const Box = ({ children }: PropsWithChildren) => {
  return (
    <section
      className="m-4"
      style={{ padding: '1em', border: '5px solid purple' }}
    >
      {children}
    </section>
  );
};
```

What if we wanted to add additional props? We can actually pass `PropsWithChildren` an argument.

```tsx
import React, { PropsWithChildren } from 'react';

type BoxProps = { color?: 'red' | 'green' | 'blue' };

const Box = ({ children, color = 'red' }: PropsWithChildren<BoxProps>) => {
  return (
    <section
      className="m-4"
      style={{ padding: '1em', border: '5px solid purple', color }}
    >
      {children}
    </section>
  );
};

const Application = () => {
  return (
    <main className="m-8">
      <Box color="green">
        Just a string.
        <p>Some HTML that is not nested.</p>
        <Box>
          <h2>Another React component with one child.</h2>
        </Box>
        <Box>
          <h2 className="mb-4">A nested React component with two children.</h2>
          <p>The second child.</p>
        </Box>
      </Box>
    </main>
  );
};

export default Application;
```

Alternatively, this will work, but the version that I showed you above is standard practice:

```tsx
const Box = ({ children, color = 'red' }: PropsWithChildren & BoxProps) => {
  // …
};
```

We'll learn how to make our own utility types like this in a bit.
