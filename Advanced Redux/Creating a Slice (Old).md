The first thing we're going to do is create a new directory called `features` followed by a file called `posts-slice.ts`.

Let's set up the shape of our state.

```ts
type PostsState = {
  posts: Post[];
};

const initialState: PostsState = {
  posts: [],
};
```

And, we'll go ahead and create our first slice.

```ts
import { createSlice } from '@reduxjs/toolkit';

const initialState: Post[] = [];

export const postsSlice = createSlice({
  name: 'posts',
  initialState,
  reducers: {},
});
```

If we take a look at the `Post` type in `global.d.ts`, we'll see that it has the following properties:

```ts
type Post = {
  id: string;
  title: string;
  body: string;
  comments: PostComment[];
};
```

Right now, our application the ability to add a post and the ability to remove a post. So, let's at least those capabilities in our reducer.

```ts
export const postsSlice = createSlice({
  name: 'posts',
  initialState,
  reducers: {
    add: () => {},
    remove: () => {},
  },
});
```

Each of those properties, is like a little miniature version of a reducer.

```ts
export const postsSlice = createSlice({
  name: 'posts',
  initialState,
  reducers: {
    add: (state) => {
      return state;
    },
    remove: (state) => {
      return state;
    },
  },
});
```

We'll start off by _just_ worrying about the ability to add a post for now.

```ts
export const postsSlice = createSlice({
  name: 'posts',
  initialState,
  reducers: {
    add: (posts, action) => {
      posts.push(action.payload);
      return posts;
    },
    remove: (state) => {
      return state;
    },
  },
});
```

We do have a small problem here, we don't have any type safety on that payload.

![](_attachments/Pasted%20image%2020221110141949.png)

Luckily, we can pull in a utility type from Redux Toolkit.

```ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit';

const initialState: Post[] = [];

export const postsSlice = createSlice({
  name: 'posts',
  initialState,
  reducers: {
    add: (posts, action: PayloadAction<Post>) => {
      posts.push(action.payload);
      return posts;
    },
    // …
  },
});
```

## Getting the actions

We can see that the slice has the following properties:

![](_attachments/Pasted%20image%2020221110142239.png)

Let's take a look at those actions.

You'll see that Redux Toolkit did a pretty good job correctly typing our creators:

```ts
CaseReducerActions<{
    add: (posts: WritableDraft<Post>[], action: PayloadAction<Post>) => WritableDraft<Post>[];
    remove: (state: WritableDraft<Post>[]) => WritableDraft<...>[];
}, "posts">
```

The `WritableDraft` is from Immer. This is a copy of the state that we can mutate. Immer will apply the immutable changes retroactively by comparing the draft to the original object. Let's export those action creators. We'll also export our reducer while we're at it.

```ts
export const { add, remove } = postsSlice.actions;

export default postsSlice.reducer;
```

## Hooking up Redux Tooklit to React

In `src/index.tsx`, we'll pull in the following from Redux Toolkit as well as the code we just wrote.

```tsx
import { configureStore } from '@reduxjs/toolkit';
import postsReducer from './features/posts-slice';

const store = configureStore({
  reducer: {
    posts: postsReducer,
  },
});
```

If you take a look at the application in the browser, you should see that the initial state is wired up.

![](_attachments/Pasted%20image%2020221110143414.png)

## Wiring the Posts

Lets's toss a simple post into our initial state to verify that everything works as expected in `src/features/posts-slice.ts`.

```ts
const initialState: Post[] = [
  { id: '1', title: 'My first post', body: 'Hello world!', comments: [] },
];
```

In `src/components/application.ts`, we can _try_ something like this, but you'll see we have some issues.

```ts
const posts: Post[] = useSelector((state) => state.posts);
```

TypeScript complains to us:

> 'state' is of type 'unknown'

Hmmm. Let's cheat for a hot minute, then we'll fix it for real.

```ts
const posts: Post[] = useSelector((state) => (state as any).posts);
```

This will work, but we'll lose _all_ of our type safety. We can inform TypeScript of what type our state conforms to.

```ts
const posts: Post[] = useSelector<{ posts: Post[] }, Post[]>(
  (state) => state.posts,
);
```

This totally, works, but you can see that this is probably going to be incredibly tedious to up update as time goes on.

## Adding better type support

An alternative would be to dynamically create types based on the output of `configureStore`.

```ts
export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

Now, we can use reference this value in `application.tsx`:

```tsx
const posts: Post[] = useSelector<RootState, Post[]>((state) => state.posts);
```

Let's go ahead and [[Strongly-typed selector and dispatch hooks]] that make all of this more convenient for us.