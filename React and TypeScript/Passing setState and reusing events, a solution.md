---
repository: https://github.com/stevekinney/inspirational-quotes
branch: with-filtering
branch: with-filtering-solution
---

First off, I desperately want to get rid of that `any` for `setFilters` in `<QuoteFilter />`. If I have taught you anything so far, it's that hovering stuff is a useful technique.

If we hover over `setFilters`, we'll see the following:

```ts
React.Dispatch<
  React.SetStateAction<{
    content: string;
    source: string;
  }>
>;
```

That's the type for `setFilters`. Cool. We can totally use that in `<QuoteFilter />` as is. Typing this correctly means that we can totally get rid of the `any` type defintion when updating those filters.

```diff
@@ -1,6 +1,11 @@
 type QuoteFilterProps = {
   filters: QuoteFilters;
-  setFilters: any; // Change me!
+  setFilters: React.Dispatch<
+    React.SetStateAction<{
+      content: string;
+      source: string;
+    }>
+  >;
 };

 const QuoteFilter = ({ filters, setFilters }: QuoteFilterProps) => {
@@ -13,7 +18,7 @@ const QuoteFilter = ({ filters, setFilters }: QuoteFilterProps) => {
           name="content"
           value={filters.content}
           onChange={(e) =>
-            setFilters((filters: any /* Ugh. */) => ({
+            setFilters((filters) => ({
               ...filters,
               [e.target.name]: e.target.value,
             }))
@@ -29,7 +34,7 @@ const QuoteFilter = ({ filters, setFilters }: QuoteFilterProps) => {
           name="source"
           value={filters.source}
           onChange={(e) =>
-            setFilters((filters: any /* Ugh. */) => ({
+            setFilters((filters) => ({
               ...filters,
               [e.target.name]: e.target.value,
             }))
```

## Using a better type for `SetStateAction`

But, like…

```ts
React.Dispatch<
  React.SetStateAction<{
    content: string;
    source: string;
  }>
>;
```

…is a bit cumbersome, right? And, we know that `QuoteFilters` looks like this:

```ts
type QuoteFilters = {
  content: string;
  source: string;
};
```

This means, we could shorten `setFilters` to:

```ts
type QuoteFilterProps = {
  filters: QuoteFilters;
  setFilters: React.Dispatch<React.SetStateAction<QuoteFilters>>;
};
```

And if, we want to pull in React, we can get it down to:

```ts
import type { Dispatch, SetStateAction } from 'react';

type QuoteFilterProps = {
  filters: QuoteFilters;
  setFilters: Dispatch<SetStateAction<QuoteFilters>>;
};
```

Not bad.

## Getting rid of the duplicative event handlers

I didn't love having to edit two event handlers that basically did the same thing. Let's pull them up.

```ts
const handleChange = (e) => {
  setFilters((filters) => ({
    ...filters,
    [e.target.name]: e.target.value,
  }));
};
```

Ugh, now our event—`e`—is `any`. Well, it's a event handler called when we change the event, right? Let's try this:

```ts
const handleChange = (e: ChangeEvent) => {
  setFilters((filters) => ({
    ...filters,
    [e.target.name]: e.target.value,
  }));
};
```

I mean, we solved the problem. It's just that we created new problems along the way. This is because not all events have `event.target` and we didn't tell React that we were dealing with `HTMLInputElement`s.

Let's hover over that `onChange` we left behind and see what we get.

On, look. React thinks that `onChange` props are the following type:

```ts
React.ChangeEventHandler<HTMLInputElement> | undefined
```

And because it knows the function is _supposed_ to be that type, it takes what we passed it inline and as long as it fits that descript types our callback appropriately.

Let's take advantage of the same idea:

```ts
const handleChange: ChangeEventHandler<HTMLInputElement> = (e) => {
  setFilters((filters) => ({
    ...filters,
    [e.target.name]: e.target.value,
  }));
};
```

**An alterative, but less good, approach**: This will _also_ work, but I like the above approach more because it fits in nicely with how we typed props earlier. I like not having to do things in different ways if I can avoid it.

```ts
const handleChange = (e: ChangeEvent<HTMLInputElement>) => {
  setFilters((filters) => ({
    ...filters,
    [e.target.name]: e.target.value,
  }));
};
```

Let's talk about another intering benefit we get from [[TypeScript's unions in React|unions]].