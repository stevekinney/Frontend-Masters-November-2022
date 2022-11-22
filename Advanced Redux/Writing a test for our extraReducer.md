This test will obviously fall at first because we're doing some good ol' TDD here and haven't written an implementation yet.

```ts
it('should unassign a user from a task when the user is removed', () => {
  const user = createUser({
    id: '1',
    realName: 'Nick',
    alterEgo: 'The Tick',
  });

  const task = createTask({
    id: '2',
    title: 'Research what kind of task The Tick would do',
    status: 'Waiting for Deployment',
    user,
  });

  const action = removeUser({ id: user.id });
  const state = tasksSlice.reducer([task], action);

  expect(state[0].user).toBeUndefined();
});
```

With the test in place, we can now [[Implementing unassigning a user from tasks when removed|implement it]].