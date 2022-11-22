## Who is this for?

This workshop is for anyone who is interested in learning how to use TypeScript with React. 

There is **no expectation** that you're familiar with TypeScript. That said, we're not going to be covering the basics of TypeScript directly.

And, I'm going to argue that's totally okay. Instead, we'll be learning it within the context of building simple components in React. You might even want to dig deeper into TypeScript after seeing some of the more advanced bits in practice first. That's how it worked for me at least.

If you *are* already super comfortable with TypeScript, I have a suspicion that you'll pick up a few tips and tricks along the way—and there are plenty of React-specific pieces that I suspect you're get a lot of value from. Additionally, I also hope that it will reinforce the fundamentals for you.

## Why should I take this course?

I think I took my first run at TypeScript like six years ago. I definitely saw the value, but it also had a learning curve that was slowing me  down a bit. I tried it—and Flow—for a spin a few more times over the years, but it wasn't until we made the choice in 2018—when we were re-writing a large React application—that my team and I decided to take the plunge.

At first, there was a copy of copy and pasting, flailing wildly and throwing pasta at the wall, and a liberal use of the `any` type. But, we eventually got our sea legs and I'm proud to say that—last I heard—that foundational code that we wrote is still in service today.

Fast forward four years and now I feel uncomfortable *not* writing in TypeScript. Even if I'm working on some little toy example applications for chats like this, I'll frequently write them TypeScript first and then boil it down for the initiatied after.

One of the points that I'm going to try to make in this course is that the developer exeperience is just *that* much better. I totally get that when you're first diving in, it can be overwhelming, but the very first time that you are in the middle of a reasonably-sized reactor and as soon as you appease the compiler, it just works—you'll be sold.

As soon as the compiler catches a silly little mistake that might have slipped by your teammates and your tests—you'll be sold. Most of the time when it seems like TypeScript is being pendantic, it's usually just trying to save you from yourself. It's like pair programming with someone who doesn't talk too much.

But, here are the quick soundbyte versions that you'll probably find in every other blog post on the Internet.

- **Type checking at *compile time*** is way better than things crashing or—worse—behaving unexpectedly at run time.
- You get a **better development experience** (read: autocomplete and/or Intellisense) because autocomplete knows more about what you’re intending on doing.
- **Large codebases stay more maintainable** because you’re able to put guardrails on how your code can be used. It's easy to make some quip about your teammates, but we all know that the real culprit is that one last commit you tried to squeeze in at the end of day.
  - But also, the upside of having a compiler that can constantly scan an increasingly growing codebase at lightning speed can't be understated.

## What's about to go down?

We're going to spend a little bit starting with the *very basics* of using TypeScript and React together. Part of this is in case this is your first rodeo with TypeScript. Part of this is because I want to lay down a decently solid foundation and make sure we're all on the some page. But, part of this is because I want to establish this **core principle**.

> Sometimes the best way to figure out how to use TypeScript in a React application is to use the tooling while you're building our your React application.

Theory is great and you should totally learn the fundamentals but there is nothing like using the thing you're building to learn more about the tools you're buidling with and I want to argue that *in this particular case* the tooling is *particularly* great way to learn about how to use TypeScript in React.

But, by the end things are going to escalate to implementing some very advanced patterns that typically leave our friends who didn't make the investment getting the fundamentals to want to throw in the towel. We're going to stop short of the *really academic* stuff that is probably only useful to people building *super generalized* libraries that are intended for a wide-range of use cases. (Types for libraries like [Styled Components](https://github.com/DefinitelyTyped/DefinitelyTyped), design systems like [Material U](https://github.com/mui/material-ui), and [React's own types](https://github.com/DefinitelyTyped/DefinitelyTyped) come to mind.) In practice, I've needed some of these patterns far less often than a lot of blog posts would seem to suggest.

Here is the high level:

- Things are going to start fairly simply for those of us who have used React before. We'll be passing props into components and setting some state.
- By the end of our time together we'll be dynamically typing extending the built-in types for DOM elements that we know and—sometimes—love. adding type safety—and more importatly, autocomplete—to higher-order components, and creating type guards that prevent our components from ending up in unexpected states.

## Getting set up

You don't need a lot to get rocking and/or rolling with this course. In fact, most of the examples and exercises have versions that are hosted on [CodeSandbox](https://codesandbox.io/). So, you probably only need a web browser and if you're reading this, you likely already have one of those.

But, let's assume that you also want to work on some of this stuff locally as well. In that case, you're going to want to have the following installed:

- [NodeJS](https://nodejs.org/). This course was tested using the latest LTS release of Node at the time (e.g. 18.12.1), but it honestly shouldn't matter much.
- You're going to need an editor of some kind. I **highly recommend** using [Visual Studio Code](https://code.visualstudio.com/). But, you likely already have opinions on this topic—but that's going to be what I'm using.

## My set up

In case you're morbidly curious about what I'm using as we spend the next several hours together, here is a quick rundown of my setup.

- **Font**: [Fira Code](https://github.com/tonsky/FiraCode). It's basically a version of Mozilla's [Fira Mono](https://fonts.google.com/specimen/Fira+Mono), but with fancy ligatures. Why? Because it looked nice when I saw in like 2015 and I'm *very* lazy.
- **Theme**: [Night Owl](https://marketplace.visualstudio.com/items?itemName=sdras.night-owl). Why? Because I've always used it and I'm *very* lazy.
- **Plugins**: I don't install a lot of plugins, but here are some of the ones that I like:  [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode).

## Who am I?

Hello! My name is [Steve Kinney](https://stevekinney.net/) and I get to lead the frontend engineering at [Temporal](https://temporal.io). We help make distributed system easy.

In the past, I was the frontend architect at [Twilio](https://twilio.com) and was the principal front-end engineer at [Twilio SendGrid](https://sendgrid.com). I started the front-end engineering program at the [Turing School of Software and Design](https://turing.io/), started a JavaScript conference called [DinosaurJS](https://dinosaurjs.org/), and wrote a book on [Electron](https://bit.ly/electronjs).

I live in Denver, Colorado. If you are so inclined to find me on the Internet, you may do so at the following locations:

- [Twitter](https://twitter.com/stevekinney)
- [Github](https://github.com/stevekinney)
- [Instagram](https://instagram.com/stevekinney)

Alright, with all of that out of the way, [let's get started](Using%20TypeScript%20without%20even%20trying.md).