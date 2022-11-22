If you look in `src/context.tsx`, you'll see that I hard-coded in the tasks. We're eventually going to switch over to using an API, but we'll continue to use this data for a little. That said, we should feed it into our store instead of straight into our React application.

Let's lift and shift that over to `src/features/tasks-slice.ts`:

```ts
import data from '../api/data.json';

const initialState: Task[] = data.tasks;
```

And now we need to feed that into our task list in `src/components/task-list.tsx`:

```ts
const tasks = useSelector((state) => state.tasks);
```

Would you look at that, TypeScript is already angry with us.

```
(parameter) state: unknown
'state' is of type 'unknown'.ts(18046)
```

Well, isn't that great. We _could_ do something like this…

```ts
const tasks = useSelector((state: { tasks: Task[] }) => state.tasks);
```

But, that's going to get _real_ tedious really fast. This certainly isn't any better.

```ts
const tasks = useSelector<{ tasks: Task[] }, Task[]>((state) => state.tasks);
```

That's even more tedious, but I think we're on to something. Hear me out. What if we:

1. Dynamically created a type based on the overall shape of the state in our store.
2. Had a way to keep `useSelector` and `useDispatch` up to date with the right types.

Well, I've got some good news for that, we can totally do that. We just need to set up some more utility functions. In this case, we're going to make some custom hooks that work like we want them to.

## Making a type-safe `useSelector`

Right now, things are simple, we only have one slice and it's an array of tasks. But keep track of that over time is going to be ridiculous. But, luckily, TypeScript is already keeping track of this—we just need to tap into its **galaxy brain**.

Let's pop over to `src/store.tsx` and try hovering over this for a hot minute:

```ts
store.getState();
```

You should see the following:

```ts
(method) Store<{ tasks: Task[]; }, AnyAction>.getState(): {
  tasks: Task[];
}
```

It's that return value that looks super interesting to us. What if we did something like this:

```ts
type ApplicationState = ReturnType<typeof store.getState>;
```

Oh, look at what we get!

```ts
type ApplicationState = {
  tasks: Task[];
};
```

If we had this, then the type of our selector _should_ be:

```ts
export type ApplicationSelector = <Selected>(
  state: ApplicationState,
) => Selected;
```

That won't _totally_ work right out of the box, becuase `useSelector` also takes a second function for checking quality and whatnot, but luckily, the React-Redux types have a utility method is spritually the same and all set up and ready for us to use.

Before we handle the hook thing, I just want to check in and make sure that your store looks like this:

```ts
import { configureStore } from '@reduxjs/toolkit';
import { tasksReducer } from './features/tasks-slice';

const store = configureStore({
  reducer: {
    tasks: tasksReducer,
  },
});

export type ApplicationState = ReturnType<typeof store.getState>;

export default store;
```

Now, we're going to make a new file called `hooks.ts`, this is mostly because I am superstitious about recursive dependencies.

```ts

```

Now, we can just swap in `useAppSelector` wherever we might have used `useSelector`. It's a thing of beauty.

## Making a type-safe `useDispatch`

The same basic approach works for dispatching actions. `dispatch` is just a method that `store` we created.

```ts
export type Dispatch = typeof store.dispatch;
```

There isn't any special type that we need to pull in, but the approach is basically the same.

```ts
import { TypedUseSelectorHook, useDispatch, useSelector } from 'react-redux';
import { ApplicationState, Dispatch } from './store';

export const useAppSelector: TypedUseSelectorHook<ApplicationState> =
  useSelector;

export const useAppDispatch: () => Dispatch = useDispatch;
```

Let's pop that into `src/components/create-task.tsx`:

```tsx
import { useAppDispatch } from '../hooks';

const CreateTask = () => {
  const [newTaskTitle, setnewTaskTitle] = useState('');
  const dispatch = useAppDispatch();

  return (
    <form
      className="create-task"
      onSubmit={(e) => {
        e.preventDefault();
        dispatch(
          addTask({
            title: newTaskTitle,
          }),
        );
      }}
    >
    </form>
  );
};
```

And we want to update `task-list.tsx` as well.

Let's go [[Removing a task|remove a task as well]].
