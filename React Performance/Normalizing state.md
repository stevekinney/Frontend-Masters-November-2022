How you define the shape of the actual data structure the represents your state can have more of an impact than going to town memoizing components—or, at the very least, in some cases, it makes it easier to even do that memoization in the first place.

1. Scanning through arrays takes time. Getting a key out of an object is way faster.
2. Could you refactor the shape of the state in order to make a more performant application?

### Workflow

- Take the orders application and optimize users by referencing a key for the user.
- **Exercise**: Have them update the products.
- Iterate over an array of keys for the list. (Using either products, orders, users, or tasks from the Packing List).
- **Exercise**: Have them do one of the others.

## Further Reading

- [Choosing the State Structure (reactjs.org)](https://beta.reactjs.org/learn/choosing-the-state-structure)
- [Description of the database normalization basics (Microsoft)](https://learn.microsoft.com/en-us/office/troubleshoot/access/database-normalization-description)
