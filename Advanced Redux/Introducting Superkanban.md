![[_attachments/superkanban-initial-state.png]]

Here is the plan:

1. Start simple in order to wrap our heads around getting Redux and TypeScript to place nicely together.
2. Up the complexity a bit and add some relations and talk a bit about some best practices with state management.
3. We'll swap out our hard-coded data for an API using `createAsyncThunk` along with TypeScript.
4. We'll swap out `createAsyncThunk` with [RTK Query](https://redux-toolkit.js.org/rtk-query/overview).

As I alluded to in the list above, we're going to refactor our state in a bit, but let's see what we're working with for starters.

```ts
type Task = {
  id: string;
  title: string;
  userId: User[id];
  columnId: StatusColumn['id'];
};

type User = {
  id: string;
  realName: string;
  alterEgo: string;
  company: string;
  skill: string;
  title: string;
  taskIds: Task['id'][];
};

type StatusColumn = {
  id: string;
  taskIds: Task['id'][];
  title: Status;
};

type Status =
  | 'Backburner'
  | 'Ready'
  | 'In Progress'
  | 'Verifying'
  | 'Waiting for Deployment'
  | 'Deployed';
```

**Up next**: [Creating a slice](Creating%20a%20slice.md)
