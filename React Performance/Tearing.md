Tearing refers to the idea of a visual inconsistency or multiple versions of a UI that can result from the same state.

If this is the first time that you're hearing the word, that's pretty reasonable. It's not really a thing that came up before concurrent rendering in React 18.

Figure that when rendering was a synchronous process, it ran to completion before anything happened. This means that the data in an [an external store](useSyncExternalStore.md) didn't really have the opportunity to change. So, it's basically impossible for the state in an external store to have changed in anyway as the rendering process moved from one component to the next.

But now—because rendering can pause for the express purpose of letting other stuff happen, it's absolutely possible that one of those things could very well be the external state in a store changing, which means that the data that components are working with could—in fact—change as the rendering process moved from one component to the next.