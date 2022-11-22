```ts
type Task = {
  id: string;
  userId: string;
  columnId: string;
  title: string;
};

type Column = {
  id: string;
  taskIds: string[];
  title: string;
};

type User = {
  id: string;
  taskIds: [];
  realName: string;
  alterEgo: string;
  company: string;
  skill: string;
  title: string;
};
```
