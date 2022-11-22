---
repository: https://github.com/stevekinney/inspirational-quotes
branch: main
endBranch: walkthrough
---

Let's take a look at [this repository](https://github.com/stevekinney/inspirational-quotes).

Things can get a little tricky when we don't have a default value to start out with. TypeScript can't help up out and make any assumptions on our behalf. Let's consider this code for a moment.

```ts
import { useEffect, useState } from 'react';
import Quotes from './quotes';
import InspirationalQuote from './quote';
import Loading from './loading';

export type Quote = {
  id: number;
  content: string;
  source?: string;
};

export const fetchRandomQuote = async () => {
  const response = await fetch(`/api/quotes/random`);
  return response.json();
};

export const fetchQuotes = async (count: number) => {
  const response = await fetch(`/api/quotes?limit=${count}`);
  return response.json();
};

const Application = () => {
  const [quote, setQuote] = useState();

  useEffect(() => {
    fetchRandomQuote().then(setQuote);
  }, []);

  if (!quote) return <Loading />;
  return (
    <main className="w-full max-w-2xl py-16 mx-auto">
      {/* <InspirationalQuote content={quote.content} source={quote.source} /> */}
      {/* <Quotes>
        <div className="grid grid-cols-2 gap-4"></div>
      </Quotes> */}
    </main>
  );
};

export default Application;
```

It doesn't even matter what the `InspirationalQuote` is just yet or what properties it takes.

Look at this:

```ts
const [quote, useQuote] = useState();
```

The type is inferred as:

```ts
const quote: undefined;
```

Well, we're passing `undefined` to `useState`. If TypeScriptn was smart enough to assume that the number we passed to the `useState` in our counter example meant that `count` was a number, then the logic follows that `quote` is of the type `undefined.`

## Solving the problem

Okay, so enough complaining—what are we going to do about it?

### Using an empty value

So, what do we do? We have a few options:

We could give it an empty value (e.g. `{ content: '', source: '' }`);

```ts
const [quote, setQuote] = useState({ content: '', source: '' });
```

This works, but it has two problems:

- We broke our loading functionality since `!quote` will never be a case.
- It's not totally clear to TypeScript that `source` is optional.

### Providing a type variable

We could also step in and help TypeScript out a bit by providing a type variable. If you hover over `useState` you'll see something that starts with:

```ts
useState<{
  content: string;
  source: string;
}>;
```

Well, we can step in and provide that service for TypeScript.

```ts
const [quote, setQuote] = useState<Quote | undefined>();
```

Now, everything works as expected and we end on the `walkthrough` branch. Let's try [an exercise](State%20and%20events,%20an%20exercise.md).