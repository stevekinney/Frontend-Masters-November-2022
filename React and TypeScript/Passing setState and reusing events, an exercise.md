---
repository: https://github.com/stevekinney/inspirational-quotes
branch: with-filtering
---

Alright, it's that time again. I added some filters, but there are some new complexities that you need to deal with. Let's take a look at the `with-filtering` branch.

```ts
type QuoteFilterProps = {
  filters: QuoteFilters;
  setFilters: any; // Change me!
};
```

Also, I don't love that I had to define the same event handler twice in the JSX output.

```ts
const QuoteFilter = ({ filters, setFilters }: QuoteFilterProps) => {
  return (
    <section className="flex gap-4">
      <label htmlFor="content-filter">
        Filter by quote content
        <input
          {/* */}
          onChange={(e) =>
            setFilters((filters: any /* Ugh. */) => ({
              ...filters,
              [e.target.name]: e.target.value,
            }))
          }
          // 👆 This feels like duplicate code.
          placeholder="Filter by quote content"
        />
      </label>
      <label htmlFor="source-filter">
        Filter by source
        {/* */}
          onChange={(e) =>
            setFilters((filters: any /* Ugh. */) => ({
              ...filters,
              [e.target.name]: e.target.value,
            }))
          }
          // 👆 This feels like duplicate code.
          placeholder="Filter by source"
        />
      </label>
    </section>
  );
};
```
