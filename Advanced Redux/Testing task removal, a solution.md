It's pretty straight-forward.

```ts
it('should remove a task', () => {
  const [task, ...rest] = initialState;
  const action = removeTask({ id: task.id });
  const state = tasksSlice.reducer(initialState, action);

  expect(state).toEqual(rest);
});
```

So, like actions and reducers look pretty bound together, right? Not really, let's look at a concept called [[extraReducers]].
