---
repository: https://github.com/stevekinney/accident-counter
branch: main
---

Your mission—if you choose to accept it—is to update our Accident Counter and get that form working with our state.

```tsx
<div>
  <form onSubmit={(e) => e.preventDefault()}>
    <input type="number" value={0} />
    <button type="submit">Update Counter</button>
  </form>
</div>
```

Here's what you're going to need to do.

- Add an additional `useState` hook that stores the value in the input field.
- On submit, update the value.

You can view the solution [here](useState,%20a%20solution.md).
