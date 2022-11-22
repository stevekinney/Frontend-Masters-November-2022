The basic strategy here is that we're going to create a new component called `Game` and get the the game and all of it's state managment away from that expensive component so that it's state changes aren't triggering `<ExpensiveComponent />` to re-render.

You should have something that looks like this:

```jsx
const Application = () => {
  return (
    <main className="flex flex-col gap-8 mx-auto my-8 w-96">
      <Game>
        <ExpensiveComponent />
      </Game>
    </main>
  );
};
```
