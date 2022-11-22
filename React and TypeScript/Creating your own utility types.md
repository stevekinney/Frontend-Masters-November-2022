Let's say you only want to get the value of a given object. You can create a custom utility function called `ValueOf`. ([Playground](https://www.typescriptlang.org/play?#code/C4TwDgpgBAaghgGwK4QPIDMA8AVAfFAXimwG0BrCEAe3WIF0BuAKCYGMqA7AZ2CgCEkwYJwDKoBNCIBvJlCgAFAEoBJALIBBRQE0AXFABEYAE4BLALZwjIfQBpZUEQFEAwqgByAEU26DXCOw4AE0trOwBfKDguKACeZiZQSH5BYQ4xEAlscElYRBQMTESIGmShUXEIXAYgA))

```ts
type ValueOf<T> = T[keyof T];
```
