Redux itself is blissfully unaware of the idea that asynchronous things exist. Redux Toolkit, on the other hand _is_ aware and ready and able to deal with your promises because it comes pre-configured with `redux-thunk`.

`createAsyncThunk` is super straightforward. We can call the function with an overall action name that will get suffixed with additional information.

```ts
const fetchTasks = createAsyncThunk('tasks/fetchTasks', async () => {
  const response: { tasks: Task[] } = await fetch('/api/tasks').then(
    (response) => response.json(),
  );
  return response.tasks;
});
```

It kind of just looks like a function. We then use `extraReducers` to listen for when it's fulfilled.

```ts
builder.addCase(fetchTasks.fulfilled, (state, action) => {
  state.push(...action.payload);
});
```

**Fun fact that will serve us later**: Before it fires that `fulfilled` action, it also fires a `pending` action.

Now, when the application starts up, we can kick off the request before out components even think about mounting in `src/index.tsx`:

```tsx
import store from './store';
import { fetchTasks } from './features/tasks-slice';

store.dispatch(fetchTasks());
```

## Exercise

Can you go implement this with [[createAsyncThunk with Users (Exercise)|users]]?
