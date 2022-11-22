It can make sense to keep all of the logic about how you select things in one place. Also, it helps hide the implementation details. We can make custom hooks with our selectors and our actions.

```ts
export const useUsers = () => {
  return useAppSelector((state) => state.users);
};

export const useUserActions = () => {
  const dispatch = useDispatch();
  return useMemo(
    () => bindActionCreators(usersSlice.actions, dispatch),
    [dispatch],
  );
};
```

And now you can use these wherever you might have used `useAppSelector` or `useAppDispatch` earlier.

In `src/components/user-list.tsx`:

```tsx
import { useUsers } from '../features/users-slice';
import CreateUser from './create-user';
import Task from './user';

const UserList = () => {
  const users = useUsers();

  return (
    <section className="user-list">
      <CreateUser />
      {users.map((user) => (
        <Task key={user.id} user={user} />
      ))}
    </section>
  );
};

export default UserList;
```

In `src/components/create-user.tsx`:

```tsx
import { useState } from 'react';
import { useUserActions } from '../features/users-slice';

const CreateUser = () => {
  const [realName, setRealName] = useState('');
  const [alterEgo, setAlterEgo] = useState('');
  const { add } = useUserActions();

  return (
    <form
      className="create-user"
      onSubmit={(e) => {
        e.preventDefault();
        add({
          realName,
          alterEgo,
        });
      }}
    >
      {/* … */}
    </form>
  );
};
```

Look, ma! No `dispatch`!

## Optional Exercise

Go ahead and try it out with the actions from the `taskSlice`.

## Moving on

From here, we can go the following directions:

- We can move on to [[Testing a slice|testing our tasks slice]].

I'm likely going to for the latter.
