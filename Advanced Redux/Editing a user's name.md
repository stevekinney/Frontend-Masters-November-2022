Let's start with a quick unit test:

```ts
import { usersSlice, updateUser } from './users-slice';

describe('usersSlice', () => {
  const initialState: User[] = [
    { id: '1', realName: 'Tony Stark', alterEgo: 'Iron Man' },
  ];

  it(`should update a user if it receives ${updateUser}`, () => {
    const action = updateUser({ id: '1', alterEgo: 'Copper Man' });
    const state = usersSlice.reducer(initialState, action);
    expect(state[0].alterEgo).toBe('Copper Man');
  });
});
```

You might have a different implementation, but here is one of mine. (Disclaimer: I could go rogue when live coding.)

```ts
export const usersSlice = createSlice({
  name: 'users',
  initialState,
  reducers: {
    // …
    updateUser: (state, action: PayloadAction<RequireOnly<User, 'id'>>) => {
      const { id, realName, alterEgo } = action.payload;
      const user = state.find((user) => user.id === id);

      if (user) {
        if (realName) user.realName = realName;
        if (alterEgo) user.alterEgo = alterEgo;
      }
    },
  },
});
```

**Director's commentary**: I had to catch myself because I am so used to _not_ mutating state that I almost replaced the object outright, which wouldn't have done me any good when Immer stepped in do all of the hard work for me. What can I say, JavaScript is hard—even if you're using TypeScript.

I stopped myself early, but now that we have a test, I'll show you the road that I almost went down for a hot minute.

```ts
// ⛔️ This is a anti-example. Do *not* do this.

const { id, realName, alterEgo } = action.payload;
let user = state.find((user) => user.id === id);

if (user) {
  user = { ...user, ...action.payload };
}
```

Good thing we have tests, because that's the kind of bug that would've been super trickly to figure out if I just changed stuff in the UI and nothing happened.

