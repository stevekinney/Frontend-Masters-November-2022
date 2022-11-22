Listen, I know how this story goes, I am going to need a lot of actions that only need the ID from an object, so let's make a utility type.

```ts
type Id<T extends { id: string }> = Pick<T, 'id'>;
```

After that, it's mostly business as usual, we can set up the `src/features/users-slice.ts`:

```ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit';
import { nanoid } from 'nanoid';

import { users } from '../api/data.json';

type DraftUser = RequireOnly<User, 'realName' | 'alterEgo'>;

const initialState = users as User[];

export const createUser = (user: DraftUser): User => {
  return { id: nanoid(), taskIds: [], ...user };
};

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
  },
});

export const usersReducer = usersSlice.reducer;
export const { addUser, removeUser } = usersSlice.actions;

export default usersSlice;
```

And, and then we need to add it to our store.

```ts
import { useSelector, TypedUseSelectorHook, useDispatch } from 'react-redux';
import { configureStore } from '@reduxjs/toolkit';

import tasks from './features/tasks-slice';
import users from './features/users-slice';

const store = configureStore({
  reducer: {
    tasks,
    users,
  },
});

// …
```

And I guess if you want to see it somewhere you should pop the component that knows about all of this stuff into the actual application.

```tsx
import CreateTask from './create-task';
import TaskList from './task-list';
import UserList from './user-list';

const Application = () => {
  return (
    <main className="application">
      <div className="side-bar">
        <CreateTask />
        <UserList />
      </div>
      <TaskList />
    </main>
  );
};

export default Application;
```

We'll set up the dispatch first in `src/components/create-user.tsx`:

```tsx
import { useState } from 'react';
import { add } from '../features/users-slice';
import { useAppDispatch } from '../store';

const CreateUser = () => {
  const [realName, setRealName] = useState('');
  const [alterEgo, setAlterEgo] = useState('');
  const dispatch = useAppDispatch();

  return (
    <form
      className="create-user"
      onSubmit={(e) => {
        e.preventDefault();
        dispatch(
          addUser({
            realName,
            alterEgo,
          }),
        );
      }}
    >
      {/* … */}
    </form>
  );
};

export default CreateUser;
```

And finally, in `src/components/user-list.tsx`:

```tsx
const users = useAppSelector((state) => state.users);
```

We can and maybe we _should_ [[Creating custom hooks|create some custom hooks]] to make our lives easier.
