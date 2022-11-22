Okay, so this is the same basic idea. But, let's go through it.

```ts
export const fetchUsers = createAsyncThunk('users/fetchUsers', async () => {
  const response: { users: User[] } = await fetch('/api/users').then(
    (response) => response.json(),
  );

  return response.users;
});
```

And then we need to add an extra reducer.

```ts
const usersSlice = createSlice({
  name: 'users',
  initialState,
  reducers: {
    addUser: (state, action: PayloadAction<DraftUser>) => {
      const user = createUser(action.payload);
      state.unshift(user);
    },
    removeUser: (state, action: PayloadAction<Id<User>>) => {
      const index = state.findIndex((task) => task.id === action.payload.id);
      state.splice(index, 1);
    },
    updateUser: (state, action: PayloadAction<RequireOnly<User, 'id'>>) => {
      const { id, realName, alterEgo } = action.payload;
      const user = state.find((user) => user.id === id);

      if (user) {
        if (realName) user.realName = realName;
        if (alterEgo) user.alterEgo = alterEgo;
      }
    },
  },
  extraReducers: (builder) => {
    builder.addCase(fetchUsers.fulfilled, (state, action) => {
      state.push(...action.payload);
    });
  },
});
```
