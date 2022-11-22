**The short answer**: By and large, it doesn't really matter. (But, that answer probably isn't good enough for your curiousity, is it?)

-   Interfaces are commonly used for defining the shape of objects and classes.
-   You might however just want to define a type of function or a type alias, types are cool too.

From [Microsoft's TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces):

> Type aliases and interfaces are very similar, and in many cases you can choose between them freely. Almost all features of an interface are available in type, the key distinction is that a type cannot be re-opened to add new properties vs an interface which is always extendable.

-   Use interfaces for public APIs since the consumer can then extend them if needed.
-   Consider using `type` for your React component props and state, for consistency and because it is more constrained.

**TL; DR**: You can extend interfaces. This is convenient, bit it can also make things more complicated. It's up to you to decide if this is a thing that makes your life better or not.

[Here](https://twitter.com/karoljmajewski/status/1082413696075382785) is a fun chart to look at.

![Types Versus Interfaces](https://pbs.twimg.com/media/DwV-oOsXcAIct2q?format=jpg&name=medium)
