I am mostly only including this one for completeness since it doesn't actually separate out the presentational component. That said, it _does_ create a reusable piece of functionaliy for us.

The big plot twist on this one is that since it doesn't have an element or component that it wraps like the [render props](./Render%20props.md) and [higher-order components](./Higher-order%20components.md), we need to use a ref that we can attach to the object in question.

```tsx
export const useMouseMove = <T extends HTMLElement>() => {
  const ref = useRef<T>(null);
  const [{ x, y }, setPosition] = useState(initialState);

  const updatePosition = useCallback(
    (event: MouseEvent) => {
      const { x, y } = getPosition(event);
      setPosition({ x, y });
    },
    [setPosition],
  );

  useEffect(() => {
    const element = ref?.current;
    element?.addEventListener('mousemove', updatePosition);
    return () => {
      element?.removeEventListener('mousemove', updatePosition);
    };
  }, [updatePosition]);

  return { x, y, ref };
};
```

## Our custom hook in action

Then we can use this hook in any component where we need to know the relative mouse position.

```tsx
export const Canvas = () => {
  const { x, y, ref } = useMouseMove<HTMLDivElement>();

  return (
    <div className="relative h-full w-full bg-primary-700" ref={ref}>
      <section className="absolute right-0 bg-primary-200 p-4 text-right">
        <p>
          <span className="font-bold">X</span>: {x}
        </p>
        <p>
          <span className="font-bold">Y</span>: {y}
        </p>
      </section>
    </div>
  );
};
```
