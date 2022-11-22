At a high-level the Context API allows you to pass data through the component hierarchy without having to pass it down through every single level manually.

One of the reasons that this is great is that if you want to move a component from one deeply-nested node of the hierarchy to another node that is a distant cousin, you don't have to undo a chain of prop-drilling and re-wire it somewhere else. With the Context API, every component in the component hierarchy has access to the same data regardless, so you can just move the component over with little to no hassle.

_Not_ using the Context API means that you're often going to have to pass data through a bunch of intermediary components that don't necessarily care about that data just to get it to the deeply-nested grandchild component that is eventually going to use it. This is cumbersome and feels like a waste of time.

You can create a new context using React's aptly-named `createContext` API. This will create two components on your behalf: a `Provider` component and a `Consumer` component.

`Provider` takes a prop called `value`. This is the value it will make to any component in the hierarchy that acceses the `Consumer`. In most modern React applications, we tend now to access the `Consumer` directly. Instead typically access it indirectly using the `useContext` hook.

Out of the box, the `Provider` component is basically only good for passing through a static value. We can't use hooks and or any of React's state management APIs outside of a React component. To get around this, a trick that you see fairly often is to wrap the `Provider` in another component that has access to all of React's state management hooks.

```jsx
const CounterProvider = ({ children }) => {
  const [count, setCount] = useState(0);

  return (
    <CounterContext.Provider value={{ count, setCount }}>
      {children}
    </CounterContext.Provider>
  );
};
```

And now we can use `CounterProvider` to wrap our component hierarchy and it will—in-turn—wrap the component tree with `CounterContainer.Provider` and all of the necessary state management accoutrements.

If you want to have access to this data _everwhere_, then you can just wrap the entire application in the provider:

```jsx
import { StrictMode } from 'react';
import { createRoot } from 'react-dom/client';
import Application, { CounterProvider } from './App';

const rootElement = document.getElementById('root');
const root = createRoot(rootElement);

root.render(
  <StrictMode>
    <CounterProvider>
      <Application />
    </CounterProvider>
  </StrictMode>,
);
```

And now your components have access to everything in the `CounterContext`.

```jsx
export default function Application() {
  const { count, setCount } = useContext(CounterContext);

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={() => setCount((n) => n + 1)}>Increment</button>
    </div>
  );
}
```

## Where to go from here

- Update Jetsetter to use the Context API.
