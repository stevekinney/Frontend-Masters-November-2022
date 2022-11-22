With our [failing test in place](Writing%20a%20test%20for%20our%20extraReducer.md), we're ready to make it pass.

```ts
export const tasksSlice = createSlice({
  name: 'tasks',
  initialState,
  reducers: {
    addTask: (state, action: PayloadAction<DraftTask>) => {
      const task = createTask(action.payload);
      state.unshift(task);
    },
    removeTask: (state, action: PayloadAction<Pick<Task, 'id'>>) => {
      const index = state.findIndex((task) => task.id === action.payload.id);
      state.splice(index, 1);
    },
  },
  extraReducers: (builder) => {
    builder.addCase(removeUser, (state, action) => {
      const userId = action.payload.id;
      for (const task of state) {
        if (task.userId === userId) {
          task.userId = undefined;
        }
      }
    });
  },
});
```

Your turn: Can you implement editing a user's name? I'll see you over in [[Editing a user's name|the solution]].
