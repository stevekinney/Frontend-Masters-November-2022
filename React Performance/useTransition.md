Let's revisit that expensive function we had when rendering a big list.

```js
const Application = () => {
  const [tasks, toggleTask] = useTasks();
  const [filters, actions] = useFilters();

  const visibleTasks = filterTasks(tasks, filters);

  return (
    // …
  );
};
```

It looks simple, but we know that function really slowed down the works.

One of the things that we can do with `useTransition` is to prioritize the stuff that matters (e.g. the responsiveness of the keyboard input) and put off the stuff that can wait for a hot minute (e.g. rendering the updated list.)

The basic idea of `useTransition` is that we're looking to say what should happen now and what can wait a bit.

```jsx
const handleChange = (e) => {
  const { name, value } = e.target;

  const newFilters = { ...filters, [name]: value };
  setFilter(newFilters);

  setTransition(() => {
    console.log('Transitioning');
    const newTasks = filterTasks(tasks, newFilters);
    setVisibleTasks(newTasks);
  });
};
```

## useDeferredValue

In a lot of ways, the relationship between `useTransition` and `useDeferredValue` is similar to that of `useCallback` and `useMemo`. One is for functions that you either want to case or put off until later and the latter are for values that you want to cache or put off for later.

Let's see what a similar solution would look like with `useDeferred` instead of `useTransition`.

```tsx
const Application = () => {
  const [tasks, toggleTask] = useTasks();
  const [filters, setFilter] = useState(initialFilters);

  const visibleTasks = useMemo(
    () => filterTasks(tasks, filters).slice(0, 100),
    [tasks, filters],
  );

  const deferredVisibleTasks = useDeferredValue(visibleTasks);
  const isPending = visibleTasks !== deferredVisibleTasks;

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFilter({ ...filters, [name]: value });
  };

  return (
    <main>
      <Filters filters={filters} onChange={handleChange} />
      {isPending && <div className="loading">Loading</div>}
      <Tasks tasks={deferredVisibleTasks} toggleTask={toggleTask} />
    </main>
  );
};
```
