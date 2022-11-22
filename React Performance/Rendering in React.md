**The short answer**: React will re-render whenever there is some kind of change to your state. That's basically the only thing that will trigger a re-render.

Now, that sounds pretty straight-forward, but that still gives us lots of wiggle room for re-renders to happen. Most of applications have _a lot_ of state that they're holding on to and managing.

Because it's not just a state change in _that_ component that triggers a re-render, once a component re-renders, it then tells all of it's children to re-render as well. And this cascades down the component hierarchy until it hits every possible dead end.

There is one quick and easy peformance optimization hiding out there, which is a state change doesn't trigger a re-render of the entire application—just the descendant components of whatever component had that initial state change. So, the smaller you can make that tree, the faster that process will go. And this is where we begin to consider the trade-offs between [lifting state up or pushing it down](Pushing%20state%20down.md).

React will move down the hierarchy regardless of pretty much anything unless we step in and use something like [React.memo](React.memo.md) in order to say, "Hey, unless the props being passed into this component are different, don't bother trying to re-render this component."

Along the way, we can use helpful hooks like `useMemo` and `useCallback` that allow us to create referrentially equal values that help us navigate around some of the edge cases that might cause `React.memo` to behave differently than we might expect.

So, like why not do this all the time? Because the act of checking _might_ be slower in a lot of cases than the act of re-rendering—specially if you just do it everywhere all of the time. Like, it could be the case that a component almost _always_ needs to re-render. If this is the case, then checking to see _if_ it should re-render is an added cost if the answer is almost always "yes."

Also, opting out of re-rendering behind your back could end up in some weird edge-cases. So, going for the naïve approach of just re-rendering and letting you step in to figure out when that's unnecessary is usually the best possible trade-off in most cases.

I should note, that the React team _is investigating_ a heuristic to auto-memoize your code. This doesn't make anything we're going to to talk about invalid, but it does mean that you _might_ get some of this for free at some point in the foreseeable future. (If you're interested, this is something you can learn a bit more about in [this talk by Xuan Huang entitled "React without memo"][without-memo].)

[without-memo]: https://www.youtube.com/watch?v=lGEMwh32soc

## The Context API

The short version of the story is that basically, you can consider `context` to be an invisible or internal prop. This means that if you've got a pure component with `React.memo` and the `context` prop changes in some way, you're going to re-render, regardless of the other props.
