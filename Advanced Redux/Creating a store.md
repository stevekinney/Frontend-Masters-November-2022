Let's export our actions and reducer from `src/features/tasks-slice.ts` for easier consumption:

```ts
export const tasksReducer = tasksSlice.reducer;
export const { addTask, removeTask } = tasksSlice.actions;

export default tasksSlice;
```

Next, we'll create `src/store.ts` and load up our reducer.

```ts
import { configureStore } from '@reduxjs/toolkit';
import { tasksReducer } from './features/tasks-slice';

const store = configureStore({
  reducer: {
    tasks: tasksReducer,
  },
});

export default store;
```

## Hooking up our store to React

We've all been here before, but let's do it one more time.

```ts
// …
import { Provider } from 'react-redux';
import store from './store';
// …

const root = ReactDOM.createRoot(
  document.getElementById('root') as HTMLElement,
);

root.render(
  <React.StrictMode>
    <ApplicationContext.Provider value={data}>
      <Provider store={store}>
        <Application />
      </Provider>
    </ApplicationContext.Provider>
  </React.StrictMode>,
);
```

I am going to leave the `ApplicationContext.Provider` in there for now because I don't to break the components that are using `useContext` with it.

Just like old times, am I right?

## Adding the action to our form

Now we just need to add—umm—`add` action to `src/components/create-task.tsx`:

```tsx
import { useState } from 'react';
import { add } from '../features/tasks-slice';

const CreateTask = () => {
  const [newTaskTitle, setnewTaskTitle] = useState('');

  return (
    <form
      className="create-task"
      onSubmit={(e) => {
        e.preventDefault();
        add({
          title: newTaskTitle,
        });
      }}
    >
      {/* … */}
    </form>
  );
};

export default CreateTask;
```

JavaScript would probably be happy with us, but TypeScript is upset. We told the action that it could receive a full task—not just the title for one. We can use a utility method here to help.

## Improving our types

The first thing we could do is just pick the properties we want off of `Task`.

```ts
type DraftTask = Pick<Task, 'title'>;
```

And now, we can update our reducer. It's still a little bit upset with us. It doesn't know that the status is of the right type. It is, but it _could_ change and TypeScript doesn't like guessing when things could be ambiguous.

You've got options:

1. Use `as const` to make sure it _doesn't_ change.
2. Create a helper function.

It's definitely a matter of taste, but I like the last of options the most.

### Creating a type-safe utility function

I can make something like this to appease TypeScript. I am using `unique

```ts
const createTask = (task: DraftTask): Task => {
  return { id: nanoid(), ...task };
}
```

### Optional: An even better utility type

I like to make a quick little utility type that isn't included in TypeScript right now. I don't require that _only_ the title is present. I just want to make sure _at least_ the title property is present. So, I'll usually make this type at some point:

```ts
type RequireOnly<T, P extends keyof T> = Pick<T, P> & Partial<Omit<T, P>>;
```

The TL;DR is as follows:

1. My type will take two arguments

- The original type and then a union of properties that I want to keep.
- I insist that the properties in that union are keys in whatever type that I'm working with.

2. Pick out all of the keys that I'm looking for and keep them from what's about to go down next.
3. Omit the properties that I called out and use `Partial` to make everything else optional.

The end result is that `DraftTask` is now:

```ts
type DraftTask = {
  id?: string | undefined;
  title: string;
  userId?: User | undefined;
  columnId: Status | undefined;
};
```

And, with the way that I wrote my `createTask` function, if those properties exist, they'll overwrite my defaults.

## Moving on

Now, just returning an action doesn't actually do anything, so we need to go ahead and `dispatch` it in order to actually make the change we wish to seek in the world.

**Up next**: [[Creating typed hooks for React]]
