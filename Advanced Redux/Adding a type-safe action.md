Let's start with the simplest possible implementation:

```ts
import { createSlice } from '@reduxjs/toolkit';

const initialState: Task[] = [];

export const tasksSlice = createSlice({
  name: 'tasks',
  initialState,
  reducers: {
    addTask: (state, action) => {
      state.unshift(action.payload);
    },
    removeTask: (state) => state,
  },
});

tasksSlice.actions.addTask;
```

Now, TypeScript isn't yelling at us, but it's also not doing exactly what we'd like. If we look at the type of that new action, we'll see that it's `any`.

```ts
(parameter) action: {
    payload: any;
    type: string;
}
```

This makes our action creator less useful than it could be:

```ts
(property) addTask: ActionCreatorWithPayload<any, "tasks/add">
```

See that `any` hiding out in there? This means that TypeScript isn't going to stop us from passing in some bogus data. We're going through this entire endeavor, because that type safety is expressly what we _want_. There is no point opting out of that in the very beginning of our time together.

Let's give everyone a bit of help and nudge it in the right direction. Redux Toolkit comes with a friendly `PayloadAction<T>` type for exactly this purpose.

```ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit';

const initialState: Task[] = [];

export const tasksSlice = createSlice({
  name: 'tasks',
  initialState,
  reducers: {
    addTask: (state, action: PayloadAction<Task>) => {
      state.unshift(action.payload);
    },
    removeTask: (state) => state,
  },
});

tasksSlice.actions.addTask;
```

Now, if we look at our action creator, we'll see that it's expecting the correct type.

```ts
(property) addTask: ActionCreatorWithPayload<Task, "tasks/add">
```

Before we wire that all up, let's look at `createSlice`'s two component parts: `createAction` and `createReducer`.

We'll start with taking a closer look at [`createAction`](createAction.md).