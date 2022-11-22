---
repository: https://github.com/stevekinney/typing-children
branch: main
endBranch: solution
---

I don't want to ruin the surprise for you, but if this workshop was solely about just strings and numbers, we'd be spending very little time together. Things can get a little bit tricky when we want to use TypeScript to specify non-primitive types—namely other React components.

It's due time that we got our first taste of that right now.

Let's start with [a super contrived example](https://codesandbox.io/s/xnnzo4?file=/src/components/application.tsx). As it stands right now, `children` has the type of `any`, which is basically an opt-out of every that TypeScript has to offer you. This isn't great.

What should you noticed about this example?

- `Box` renders `children`.
- It can render more than one child.
- That child can be another React component.
- That child can be a standard HTML element.

But, what can we use to specify that a given prop should be another React component?

Off the top of my head, here are some things that you could try.

- `JSX.Element`
- `JSX.Element[]`
- `JSX.Element | JSX.Element[]`
- `React.ReactNode`
- `React.ReactChildren`
- `React.ReactChild[]`

Why don't you take it for a spin and see what works best for you? You can use [this sandbox](https://codesandbox.io/s/xnnzo4?file=/src/components/application.tsx).

```tsx
type BoxProps = { children: any /* 👈 Get rid of this! */ };

const Box = ({ children }: BoxProps) => {
  return (
    <section
      className="m-4"
      style={{ padding: '1em', border: '5px solid purple' }}
    >
      {children}
    </section>
  );
};

const Application = () => {
  return (
    <main className="m-8">
      <Box>
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

When you're ready, we'll do some exploring to figure which one of these options is the way forward in [the solution](Typing%20children,%20a%20solution.md).
