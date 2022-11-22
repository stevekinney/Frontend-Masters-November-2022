If you're wondering what a slice is, I think that's a reasonable question to ask. *But*, I'll tell you what—**let's make one** and then we'll pull it apart and talk about it afterwards. But, I'll give you the simple version right now—it's kind of like a nice little package of actions (and action creators, really), reducers, and all of the other little fun things that you might have—tediously—done my hand before in the past.

The first thing we're going to do is create a new directory called `features` followed by a file called `tasks-slice.ts`.

Boom. That was easy, right? It stands to reason that that we'll probably hold on to some tasks here, right? So, what does a task look like?

```ts
const initialState: Task[] = [];
```

I'm making the type explicit because I want TypeScript to know that the empty array will eventually hold tasks. If I omit this, my type will get a bit funky.

TypeScript will assume the following type:

```ts
const initialState: any[];
```

And then we'll lose like all of the help that TypeScript was supposed to give us. Let's at least start strong, shall we?

## A slice of tasks

With that, let's create the world's simplest slice.

```ts
import { createSlice } from '@reduxjs/toolkit';

const initialState: TasksState = {
  tasks: [],
};

export const tasksSlice = createSlice({
  name: 'tasks',
  initialState,
  reducers: {},
});
```

## Creating some actions

One of the cool things about Redux Toolkit is that it'll automatically create actions on our behalf when we create our reducer based on the properties that we give to the reducer.

Let's give it a few actions just to take it for a spin.

```ts
import { createSlice } from '@reduxjs/toolkit';

const initialState: Task[] = [];

export const tasksSlice = createSlice({
  name: 'tasks',
  initialState,
  reducers: {
    addTask: (state) => state,
    removeTask: (state) => state,
  },
});
```

If you have this funny sense that like actions and reducers seem tied to one another, that's a good sense. Nurture that sense. But, I'll also confort you by telling you that they're not as tightly-bound as it may seem. They're only bundled together when it's convenient.

If we hover over any of the properties we put on the reducer object, you'll see—that other than some ceremony for [Immer](Immer.md)—it understands what the types ought to be for free.

```ts
(property) addTask: (state: WritableDraft<Task>[]) => WritableDraft<Task>[]
```

Immer creates a `WritableDraft`, which is a copy of our state that we can mutate. After we're done, it takes care of making the immutable changes to the object.

Our `tasksSlice` has a property called `actions`. If we take a look at `tasksSlice.actions.addTask`, you'll see that we have a properly-typed action ready to go.

```ts
(property) addTask: ActionCreatorWithoutPayload<"tasks/add">
```

We know that this action is going to need a payload eventually, but you'll be excited to know that Redux Toolkit will figure most of that out for us on our behalf. Let's go look at [adding a task](Adding%20a%20type-safe%20action.md).

**Up next**: [Adding a type-safe action](Adding%20a%20type-safe%20action.md)
