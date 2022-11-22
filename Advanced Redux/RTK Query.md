Just like we have slices, we have services and just like we have `createSlice`, we have `createApi`:

```ts
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';

export const tasksApi = createApi({
  reducerPath: 'tasksApi',
});
```

We can give a `baseQuery` utility. The one that ships with RTK Toolkit is general purpose and works with most RESTful JSON APIs. The cool power is you can swap it out if you have like GraphQL or something special like that.

```ts
export const tasksApi = createApi({
  reducerPath: 'tasksApi',
  baseQuery: fetchBaseQuery({ baseUrl: '/api' }),
});
```

Next up, instead of `reducers`, we have endpoints. And this is also a super powerful concept that gives us our familiar builder.

```ts
export const tasksApi = createApi({
  reducerPath: 'pokemonApi',
  baseQuery: fetchBaseQuery({ baseUrl: '/api/' }),
  endpoints: (builder) => ({
    getTasks: builder.query<Task[], void>({
      query: () => 'tasks',
    }),
    getUsers: builder.query<User[], void>({
      query: () => 'users',
    }),
  }),
});
```

## Pulling it into your store

Add it to your reducer:

```ts
const store = configureStore({
  reducer: {
    tasks: tasksReducer,
    users: usersReducer,
    [tasksApi.reducerPath]: tasksApi.reducer,
  },
});
```

We're also going to add some middleware that it provides.

```ts
const store = configureStore({
  reducer: {
    tasks: tasksReducer,
    users: usersReducer,
    [tasksApi.reducerPath]: tasksApi.reducer,
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(tasksApi.middleware),
});
```

## Getting the hooks.

So, now check this out.

```ts
export const { useGetTasksQuery, useGetUsersQuery, useGetStatusColumnsQuery } =
  tasksApi;
```

## Wiring it up

```tsx
import { useMemo } from 'react';
import { useGetTasksQuery } from '../services/tasks-service';
import Loading from './loading';
import Task from './task';

const TaskList = () => {
  const { data, isLoading } = useGetTasksQuery();

  const tasks = useMemo<Task[]>(() => {
    if (!data) return [];
    return data.tasks;
  }, [data]);

  return (
    <section className="task-list">
      <Loading loading={isLoading} />
      {tasks.map((task) => (
        <Task key={task.id} task={task} />
      ))}
    </section>
  );
};

export default TaskList;
```

## Transforming the response

But that `useMemo` is kind of gross, right?

Well, there is more that we can do here as well!

```ts
export const tasksApi = createApi({
  reducerPath: 'tasksApi',
  baseQuery: fetchBaseQuery({ baseUrl: '/api/' }),
  endpoints: (builder) => ({
    getTasks: builder.query<Task[], void>({
      query: () => 'tasks',
      transformResponse: (response: TasksResponse) => {
        return response.tasks;
      },
    }),
    getUsers: builder.query<{ user: User[] }, void>({
      query: () => 'users',
    }),
    getStatusColumns: builder.query<{ columns: StatusColumn[] }, void>({
      query: () => 'columns',
    }),
  }),
});
```

## Polling

Not super useful for our application, but interesting none-the-less.

```ts
const TaskList = () => {
  const {
    data: tasks,
    isLoading,
    isFetching,
  } = useGetTasksQuery(undefined, {
    pollingInterval: 5000,
  });

  return (
    <section className="task-list">
      {isFetching && <p>Fetching more…</p>}
      <Loading loading={isLoading} />
      {tasks && tasks.map((task) => <Task key={task.id} task={task} />)}
    </section>
  );
};
```

[[Handling RTK Query from other reducers]]