Okay, here is the **problem statement**: We want to unassign a user from a task when that user is removed.

But, that seems hard, right? Our slices are perfect little pieces of encapsulation. That's not the case, actually. One of the things that I _really_ like about Redux is _every_ action passes through _every_ reducer. This means that radically different parts of your state tree can respond to the same action.

I want you to make me a promise that you'll think about this next time you feel compelled to co-located the same data in multiple places in your state tree.

In addition to `reducers`, slices have another—extra—property called `extraReducers`.

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
  extraReducers: (builder) => {},
});
```

Let's stop and take a look at what that `builder` has to offer us. In this particular case, it has the type `ActionReducerMapBuilder<Task[]>`.

It has the following methods:

- `addCase`: This is the main one, it adds cases for a given action type. These need to come first.
- `addMatcher`: This is a more flexible version of `addCase`. You can do fancy stuff like `action.type.endsWith("/rejected")` and catch all of the actions that—umm—end with "/rejected".
- `addDefaultCase`: This one—if you need to use it all—shows up at the bottom of your extra reducer logic. It steps in if none of the other methods above caught the action.

Adding a case for the `removeUser` action would look something like this:

```ts
export const tasksSlice = createSlice({
  // …
  extraReducers: (builder) => {
    builder.addCase(removeUser, (state, action) => {});
  },
});
```

But, you know what's really cool? If you hover over `action`, you'll see that it's correctly typed on our behalf. We don't have to guess what the payload is.

```ts
(parameter) action: {
  payload: Id<User>;
  type: "users/removeUser";
}
```

Let's write a [[Writing a test for our extraReducer|write a test]] for this.
