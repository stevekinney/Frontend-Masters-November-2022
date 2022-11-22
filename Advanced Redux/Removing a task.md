This is pretty simple, we can splice the task from the array in order to remove it in `src/features/tasks-slice.ts`:

```ts
export const tasksSlice = createSlice({
  name: 'tasks',
  initialState,
  reducers: {
    addTask: (state, action: PayloadAction<DraftTask>) => {
      state.unshift(createTask(action.payload));
    },
    removeTask: (state, action: PayloadAction<Pick<Task, 'id'>>) => {
      const index = state.findIndex((task) => task.id === action.payload.id);
      state.splice(index, 1);
    },
  },
});
```

I'm picking the `id` property off the `Task` type because I fear things changes from underneath me.

Now, we just need to dispatch the action in `src/components/task.tsx`:

```tsx
const Task = ({ task }: TaskProps) => {
  const dispatch = useDispatch();

  return (
    <article className="task">
      {/* … */}
      <button
        className="destructive small"
        onClick={() => dispatch(removeTask({ id: task.id }))}
      >
        Remove
      </button>
      {/* … */}
    </article>
  );
};
```

Now thast we have some basic operations for a user, why don't *you* go ahead and add a user? I'll see you over in [[Creating a user slice, a solution|the solution]].
