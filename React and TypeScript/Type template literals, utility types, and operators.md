To pull it all together, I'll sometimes use a technique like this to make my life easier.

```ts
type User = {
  firstName: string;
  lastName: string;
  age: number;
};

type ActionTypes = `update-${keyof User}`;

type Actions<T, K extends keyof T & string> = {
  type: `update-${K}`;
  payload: T[K];
};

type UpdateNameAction = Actions<User, 'firstName'>;
```

You can see it in action [here](https://www.typescriptlang.org/play?#code/C4TwDgpgBAqgzhATlAvFA3gKCjqAzAS0TmADkBDAWwgC4oTECA7AcwG5tcAbcki6ug2btOOci1pQmAV0oAjJBwC+mTKEhQAggGNgBAPZMAKuAhxUUAAbSwAE3LAIAWgAk6ANYQQ+vLASIlSw41Uy1dAyY4AB4jABooAGkoCAAPRyZbc09vXyMoADJ6YEZWAD4LLFwodUlrOwdnNwTA2NEoMHIQLn1yWzojAG0EgF1MFRCNGHrHfggdPUMLeYjo+CR4gHJCYjIqCA3SoA).
