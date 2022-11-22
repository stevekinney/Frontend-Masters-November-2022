Let's write some tests to make sure that everything works as expected. I'm going to make a new file called `src/features/tasks-slice.test.ts`.

Just in case I forgot in a previous session, let's make sure we exported `createTask`:

```ts
export const createTask = (task: DraftTask): Task => {
  return { id: nanoid(), ...task };
};
```

And now we can write a simple test in `src/features/tasks-slice.test.ts`:

```ts
import { add, createTask, tasksSlice } from './tasks-slice';

describe('tasksSlice', () => {
  const initialState: Task[] = [
    { id: '1', title: 'Write some tests', status: 'In Progress' },
    { id: '2', title: 'Make sure the tests pass', status: 'Verifying' },
  ];

  it('should add a task', () => {
    const task = createTask({ title: 'Profit' });
    const action = add(task);
    const state = tasksSlice.reducer(initialState, action);

    expect(state).toEqual([task, ...initialState]);
  });
});
```

I pull in `createTask` because I don't want to keep track of what number nanoid was going to create. I could have also written the test like this:

```ts
import { add, tasksSlice } from './tasks-slice';

describe('tasksSlice', () => {
  const initialState: Task[] = [
    { id: '1', title: 'Write some tests', status: 'In Progress' },
    { id: '2', title: 'Make sure the tests pass', status: 'Verifying' },
  ];

  it('should add a task to the beginning of the list', () => {
    const title = 'Profit';
    const action = add({ title });
    const state = tasksSlice.reducer(initialState, action);

    expect(state.length).toBe(initialState.length + 1);
    expect(state[0].title).toEqual(title);
  });
});
```

Why don't you test out removing a task and then we'll meet back up and talk about [[Testing task removal, a solution|a solution]].
