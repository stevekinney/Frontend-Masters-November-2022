## Who is this for?

This is a workshop for anyone who is already familiar with React and wants to take their understanding of React to the next level. We'll learn how React updates and re-renders our applications internally as well as how we can provide it a little bit of guidance in order to do as little work as possible.

**After all**: [not doing stuff is definitely faster than doing stuff](Steve's%20Golden%20Rule%20of%20Performance.md).

In addition to just general performance advice, you'll learn a lot more about how React works. 

## Why should I take this course?

- You're looking for more insight into *how* React works.
- You want to build a toolset for diagnosing performance issues in React.
- You want to learn some best practices when building React applications in hopes of avoiding some performance issues down the road.

## What's about to go down?

We're going to take a few different examples and see if we can track down the times that it re-renders when it doesn't need to. I'm going to be honest with you. These applications are not particularly slow to begin with, but they possess all of the characteristics that would cause them to slow your application at scale. Rarely are we lucky enough that it's just one major things slowing down our application. Instead, it's often a bunch of smaller issues that pile up on each other over the course or months or years.

That said, once you get comfortable with the concepts and with the ability to diagnose issues, you'll find that you're able walk back performance bottlenecks in less time than it took them emerge.

This workshop is primarily focused on React perfromance, so tracking down re-renders is the name of the game. But, we'll also look at how fundamental concepts like the structure of your state can do more good for the performance of your application than going around an memoizing components and values throughout your application. That said, there is obviously more to performance than just React re-rendering. We'll touch on some of these ides on occasion, but we will try to focus on React for the most part.

Finally, we'll explore some new (and upcoming) React features—such as Suspense, transitions, and deferred values—that will allow you to even further optomize the performance of your application.

## Getting set up

You don't need a lot to get rocking and/or rolling with this course. 

But, let's assume that you also want to work on some of this stuff locally as well. In that case, you're going to want to have the following installed:

- [NodeJS](https://nodejs.org/). This course was tested using the latest LTS release of Node at the time (e.g. 18.12.1), but it honestly shouldn't matter much.
- You're going to need an editor of some kind. I **highly recommend** using [Visual Studio Code](https://code.visualstudio.com/). But, you likely already have opinions on this topic—but that's going to be what I'm using.

## My set up

In case you're morbidly curious about what I'm using as we spend the next several hours together, here is a quick rundown of my setup.

- **Font**: [Fira Code](https://github.com/tonsky/FiraCode). It's basically a version of Mozilla's [Fira Mono](https://fonts.google.com/specimen/Fira+Mono), but with fancy ligatures. Why? Because it looked nice when I saw in like 2015 and I'm *very* lazy.
- **Theme**: [Night Owl](https://marketplace.visualstudio.com/items?itemName=sdras.night-owl). Why? Because I've always used it and I'm *very* lazy.
- **Plugins**: I don't install a lot of plugins, but here are some of the ones that I like:  [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode).


## Who am I?

Hello! My name is [Steve Kinney](https://stevekinney.net/) and I run the frontend engineering at [Temporal](https://temporal.io). We help make distributed system easy.

In the past, I was the frontend architect at [Twilio](https://twilio.com). I started the front-end engineering program at the [Turing School of Software and Design](https://turing.io/), started a JavaScript conference called [DinosaurJS](https://dinosaurjs.org/), and wrote a book on [Electron](https://bit.ly/electronjs).

I live in Denver, Colorado. If you are so inclined to find me on the Internet, you may do so at the following locations:

- [Twitter](https://twitter.com/stevekinney)
- [Github](https://github.com/stevekinney)
- [Instagram](https://instagram.com/stevekinney)
