Start with [this playground](https://www.typescriptlang.org/play?jsx=1&ssl=14&ssc=1&pln=15&pc=1#code/C4TwDgpgBAwgFgQwE4IMbAkmAbBBnPKAXigHIB3ZJAeyVKgB8ywFcATASwDt6mKOAXsja8yqbJg6pSAbgBQc0JCgAZBOXgJqhEqVzkAZgFdso0lwhHgKU4zGJqwKbMXhoAcWrU2AUQBuHNjEZADmXiJ25pbWrGYQAabyrsqaKOiYwQDeclBQXAgAthAAXFB41twh8rlgNAYQBBzUXKWpaBhYuATVUKwcIVxFXMCl5UiVMlAA9FNQAOrQlMNQwNRQqIgh0MBwHHgAhHIAvgqozeVlGH4lsIhpHVk5eYU3pADKVxCkADRPtdT1RrNUr8IRIES-XJ9AZDEb2LROVAAWjC3h+xxkQA).

## Initial State

```ts
type CharacterClass = "warror" | "paladin" | "wizard" | "cleric";

type LawChaos = "lawful" | "neutral" | "chaotic";
type GoodEvil = "good" | "neutral" | "evil";

type Character = {
  name: string;
  profession: CharacterClass;
  alignment: string; // We want to chage this!
};

const steve: Character = {
  name: "Steve",
  profession: "wizard",
  alignment: "chaotic-good",
};
```

## Solution

```ts
type CharacterClass = "warror" | "paladin" | "wizard" | "cleric";

type LawChaos = "lawful" | "neutral" | "chaotic";
type GoodEvil = "good" | "neutral" | "evil";

type Alignment = `${LawChaos}-${GoodEvil}`;

type Character = {
  name: string;
  profession: CharacterClass;
  alignment: Alignment;
};

const steve: Character = {
  name: "Steve",
  profession: "wizard",
  alignment: "chaotic-good",
};
```

## Your Turn

Visit [this sandbox](https://www.typescriptlang.org/play?jsx=1&ssl=30&ssc=1&pln=31&pc=1#code/C4TwDgpgBAahBOwCWBjAhgGwIIaQcwDsBbCA4KAXigHJgB7MaqAHxpVOASdeoCM7g9ItQDcAKFCQoACTrwkALzoFMOfMQ6UaGCADNg3Nhy4sa8vAAsD4ieGgAhOgA8tAbzFQoTgFxQCAVyJeBHFPEF8AoJCPKEx1EjJfAGdgeQI8ESgAeiyoABkIAySoIjQAa2hgCyRi4MEEADoxAF8bFGUU2N9HFyp3Tx8oAEYABgAaGPDh8Zi4wgTgX1oGAFp2Mi4J1rExdoJO3m7nNxjBgCYZsN8Lic85jUSafkE6IhXzK2otto7yJIs6P4MAATezwCDlI69KD9LzXS5QKY3Wa4eYcJY6fTvfCfb47Padf6AkFgiFlLDAABiSHgKXs-mAAFEAG4cfyYDAgADqcjKUJOA18owRU2Ft1iqIeiyMG3gXxaIiAA).

Make a template literal type that eventually breaks the second two examples.

```ts
type VerticalAlignment = "top" | "center" | "bottom";
type HorizonalAlignment = "left" | "center" | "right";

type Box = {
  x: number;
  y: number;
  alignment: string; // Let's make this better.
};

const a: Box = {
  x: 10,
  y: 10,
  alignment: "top-center",
};

const b: Box = {
  x: 20,
  y: 20,
  alignment: "bottom-right",
};

const shouldBreak: Box = {
  x: 20,
  y: 20,
  alignment: "left-right",
};

const shouldBreakAtFirstButEventuallyWork: Box = {
  x: 100,
  y: 100,
  alignment: "center",
};
```

### Extension

-   Could we get 'center' to work instead of 'center-center'?
-   And what if we wanted to get rid of 'center-center'?