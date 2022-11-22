As I mentioned before, you'll also notice that the thunk also dispatches a `pending` action. We can use that to show a loading state during the initial or subsequent fetches.

We probably need to hold on to this in our state somewhere. Good thing we pushed entity down a bit, right?

We can update our two builders as follows:

```ts
builder.addCase(fetchTasks.pending, (state, action) => {
  state.loading = true;
});

builder.addCase(fetchTasks.fulfilled, (state, action) => {
  state.loading = false;
  state.entities.push(...action.payload);
});
```

And I'm going to opt to build out our `useTasks` hook.

```ts
export const useTasks = () => {
  const tasks = useAppSelector((state) => state.tasks.entities);
  const isLoading = useAppSelector((state) => state.tasks.loading);
  return useMemo(() => [tasks, isLoading] as const, [tasks, isLoading]);
};
```

And then we need to update `task-list.ts`.

```ts
import { useTasks } from '../hooks';
import Loading from './loading';
import Task from './task';

const TaskList = () => {
  const [tasks, isLoading] = useTasks();

  return (
    <section className="task-list">
      <Loading loading={isLoading} />
      {tasks.map((task) => (
        <Task key={task.id} task={task} />
      ))}
    </section>
  );
};

export default TaskList;
```

## Dealing with Rejection

```ts
export const fetchPokemonByName = createAsyncThunk<Pokemon, string>(
  'pokemon/fetchByName',
  async (name, { rejectWithValue }) => {
    const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${name}`);
    const data = await response.json();
    if (response.status < 200 || response.status >= 300) {
      return rejectWithValue(data);
    }
    return data;
  },
);
```
