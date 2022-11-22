In this course, we're going to pick up where [Redux Fundamentals](https://frontendmasters.com/courses/redux-fundamentals/) left off. In the first course we look at how Redux works under the hook, using the `react-redux`'s hooks and its more-veteran Connect API to connect it to React applications, and took a brief look at React Toolkit.

In this course, we're going to start out with a deeper look at using Redux Toolkit before looking at using TypeScript in our Redux applications, unit testing with Redux, writing custom middleware, code splitting, and working with React Router. Finally, we'll look at solving some advanced, asynchornous state-management problems with Redux observable.

## Redux Toolkit and TypeScript

- [Introducting Superkanban](Introducting%20Superkanban.md)
- [Creating a slice](Creating%20a%20slice.md)
- [Adding a type-safe action](Adding%20a%20type-safe%20action.md)
- An aside: [createAction](createAction.md)
- Mini-exercise: [[createAction Exercise|createAction]]
- An aside: [createReducer](createReducer.md)
- Mini-exercise: [[createReducer Exercise|createReducer]]
- [Creating a store](Creating%20a%20store.md)
- [Creating typed hooks for React](Creating%20typed%20hooks%20for%20React.md)
- [Removing a task](Removing%20a%20task.md)
- [Creating a user slice, a solution](Creating%20a%20user%20slice,%20a%20solution.md)
- [Creating custom hooks](Creating%20custom%20hooks.md)
- Optional Mini-exercise: [[Slices and hook, an exercise|Slices and hooks]]

## Unit Testing Reducers

- [Testing a slice](Testing%20a%20slice.md)
- [Testing task removal, a solution](Testing%20task%20removal,%20a%20solution.md)
- Bonus: [[Testing adding and remove users]]

## extraReducers

- [[extraReducers]]
- [[Writing a test for our extraReducer]]
- [[Implementing unassigning a user from tasks when removed]]
- Exercise: Editing a user's name
- Solution: [Editing a user's name](Editing%20a%20user's%20name.md)
- [[Mutliple reducers listening to the same action]]

## Towards asynchrony

- [[createAsyncThunk]]
- [[createAsyncThunk with Users (Exercise)]]
- [[Loading states]]

## RTK Query

- [RTK Query](RTK%20Query.md)

## Redux middleware

- [Middleware](Middleware.md)
- [[Enhancers]]

## Advanced Patterns

- [[Redux with React Router]]
- [[Usage with Streams]]
- [[Undo history]]