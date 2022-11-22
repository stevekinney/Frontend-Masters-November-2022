We hear a lot of talk about how we're supposed to lift state up in our React applications. This is all well and good, but every time we change some state in our React applications, we call on React to cascade down the hierarchy and reconcile all of the changes that may or may not have occured in order to figure out what's re-rendering. One useful strategy for improving the performance of your application is just to—when in doubt—push the state down to the lowest common denominator. This will keep the tree as small as possible.

And, as we know from [Steve's Golden Rule of Performance](Steve's%20Golden%20Rule%20of%20Performance.md), doing less stuff takes less time.

## A Quick Win

![](_attachments/Pushing%20State%20Down.gif)

The first thing that jumps out at us is that simply typing into the input field causes almost every component in the application to re-render. This seems like a bit much if you ask me. The good news, is that we can easily address this issue and get a quick performance win by spending a hot minute meditating on our component hierarchy.

Earlier, we talked about how triggering a state change in one component compels React to work its way down the tree, re-rendering every component along the way.

As it stands right now, we're storing the new item name in `Application`, which absolutely needs to know the of the new item when it's time to add it to the list. But, you know what it doesn't care about? Every single keystroke leading up to the user hitting that "Add New Item" button. Sure, `NewItem` cares about this state, since it needs to update the input field, but `Application` is happy to wait until we're ready to add the new item to find out what the name is.

One of the easiest and most fool-proof strategies for reducing the number of re-renders in your application is to push your state down to the lowest level possible—there by creating the smallest re-render tree that you can get away with.

**Mini-Exercise**: Let's do the following:

- Add `useState` to `NewItem`.
- Take `newItemName` and `setNewItemName` out of `Application` and push it down into `NewItem`.
- Remove those props from `NewItem`.

Our change should look something like this:

![](_attachments/Pushing%20State%20Down%20Diff.png.png)

We should new see that only the `NewItem` component re-renders. That was pretty simple, right?

![](_attachments/Refactored%20New%20Item.gif)