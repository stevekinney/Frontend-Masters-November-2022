Since we covered that changing state is _the thing_ that triggers a re-render in React, it makes sense to think that we should probably do that as little as possible, right? That sounds reasonable.

One of the easiest ways to break this rule is to start storing stuff in state that you could have easily computed using what you already had in state. 

We also know that `useMemo` will allow you to calculate those values and then hold on to them unless the values that you're computing that result from change. Applying this approach can drastically cut down on the number of re-renders in your application.

Things you probably don't need to store in state:

- A `boolean` value defining whether or not a cart has anything in it.
- A number of how many items are in the cart.
- The total price of everything in the cart.
