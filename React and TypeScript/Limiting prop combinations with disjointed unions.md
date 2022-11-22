**Initial idea**: Do something similar to function overloads for the components shown above.

**Example idea**: A select component that allows for a `multiple` property. If `multiple` is true then `value` should be an array, if not then it should be a single value. We can limit it to a string for now. (This should also be true for the `onChange` event handler).

([Source](https://blog.andrewbran.ch/expressive-react-component-apis-with-discriminated-unions/#props-unions-to-the-rescue))

**Example idea**: You can [use render props to determine how groups should be rendered](https://blog.andrewbran.ch/expressive-react-component-apis-with-discriminated-unions/#going-deeper-with-the-distributive-law-of-sets).

