React gives us three primary ways to skip rendering a component:

1. [`React.memo()`](https://beta.reactjs.org/apis/react/memo), which works with both function- and class-based components.
2. The [`shouldComponentUpdate`](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) method on class-based components.
3. Inheriting from [`React.PureComponent`](https://reactjs.org/docs/react-api.html#reactpurecomponent) as opposed to `React.Component` when using class-based components.

We're going to primarily focus on `React.memo` as our primary method for optimizing render performance in React since it works for both functional- and class-based components while the latter two only work when using class-based components.

`React.memo()` is a higher-order component. It takes your component and returns a wrapped component. This new, wrapper adds in the ability to see if any of the props have changed. If they haven't changed, then React will skip rendering the component and any of its children.