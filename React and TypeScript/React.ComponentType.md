**TL;DR**: `React.ComponentType<P>` is a useful way to say "Hey, this should be a React component that takes props that I specify as `P`."

For example:

```ts
type StatusLabel = 'Loaded' | 'Loading';
type ComponentWithStatusLabelProps = React.ComponentType<{
  status: StatusLabel;
}>;

const WithLoadingStatus = ({
  Component,
}: {
  Component: ComponentWithStatusLabelProps;
}) => {
  const [status, setStatus] = useState<StatusLabel>('Loading');

  useEffect(() => {
    setTimeout(() => {
      setStatus('Loaded');
    }, 3000);
  });

  return <Component status={status} />;
};

const Okay = ({ status }: { status: StatusLabel }) => <p>{status}</p>;
const NotOkay = ({ status }: { status: number }) => <p>{status}</p>;

export default function Application() {
  return (
    <div className="App">
      <WithLoadingStatus Component={Okay} />
      <WithLoadingStatus Component={NotOkay} />
    </div>
  );
}
```

`WithLoadingStatus` will _only_ take components that have a `status` prop that is of the `StatusLabel` type.

## Extra credit: A needlessly pedantic deep dive

**Nota bene**: We're not going to cover this in the course. This is just an exercise for the reader if you find yourself interested in exploring further.

Okay, if we look at [the type definition](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/6ca99826a8a8b6f6b9b89644602ae96d0ce024e9/types/react/index.d.ts#L75), this one isn't particularly helpful.

```ts
type ComponentType<P = {}> = ComponentClass<P> | FunctionComponent<P>;
```

So, let's drill down a bit into [`FunctionComponent`](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/6ca99826a8a8b6f6b9b89644602ae96d0ce024e9/types/react/index.d.ts#L519-L525).

```ts
interface FunctionComponent<P = {}> {
  (props: P, context?: any): ReactElement<any, any> | null;
  propTypes?: WeakValidationMap<P> | undefined;
  contextTypes?: ValidationMap<any> | undefined;
  defaultProps?: Partial<P> | undefined;
  displayName?: string | undefined;
}
```

It's really those first two lines that we care about these days:

```ts
interface FunctionComponent<P = {}> {
  (props: P, context?: any): ReactElement<any, any> | null;
  // …
}
```

This mean that's its a function that takes whatever props we expect and spits out a [`ReactElement`](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/6ca99826a8a8b6f6b9b89644602ae96d0ce024e9/types/react/index.d.ts#L140-L144).

```ts
interface ReactElement<
  P = any,
  T extends string | JSXElementConstructor<any> =
    | string
    | JSXElementConstructor<any>,
> {
  type: T;
  props: P;
  key: Key | null;
}
```
