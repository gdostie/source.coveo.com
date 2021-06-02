---
layout: post

title: "How to write good UI tests"

tags: [UI, tests, react, testing library, frontend]

author:
  name: Gaël Dostie
  bio: Software Developer, Administration Console UI team
  image: gdostie.jpeg
---

You feel like you do a decent job at creating new user intefaces or modifying existing ones, but writing tests to cover your changes takes you more time than to write the actual source code. You have experience writing tests for clear cut units of code like functions or classes, but it seems that testing UI is just too different. Having lost all intuition about what needs to be tested, what needs to be mocked, how to split your test cases or even how to write a simple assertion, you feel lost and unproductive. If you can relate to any of the above, this article is for you.

In this post, we will showcase a testing philosophy that will make you look forward to writing tests for UI. Not only because your tests will grant you confidence and improve the quality of the code you produce but also quite frankly because it will become an enjoyable thing to do.

<!-- more -->

## Why do we write tests

Lets start with _why_. If you think about it, when we create or change something visual, we surely look at it and interact with it manualy while changing its source code until we are satisfied with the result. So why do we even bother creating an additional scripted test that doesn't have eyes to tell us if something looks good or bad?

A simple answer to that question is that scripted tests (unit, functional, end-to-end, all of them, we'll get to that part later) provide us with a level of confidence that the UI (still) works as intended.

> In a sense, scripted tests are a written garantee that the UI under test respects a given set of behaviours.

Its important to always keep that in mind while testing, because it helps us define what needs to be tested. Which brings us to my next point.

## What do we need to test

There are already a handfull of good articles out there about the differents levels of tests that exists: static, unit, integration, functional, end-to-end, manual, mutants, performance, etc. Those articles usually offer good suggestions about the amount of each of them you should aim to have based on your needs. However, in the universe of UI tests, the line between some of those categories can become pretty blurry. What is a unit? Is a button a unit? What if the screen shows a form with a submit button, is that a unit or a functionality?

The best way to make sense out of all that confusion and remain productive is to not waste energy on putting a label on the kind of tests we're doing and just remember why we're writing tests. We want to provide a garantee about certain behaviours. Simply put, we test behaviours. Whether the behaviour is a unit or a functionality, we don't really mind, we only care that it works. If the UI under test offers no noticeable behaviours from a user standpoint (and this is really the key), then we don't need nor want to write a test for that specific UI.

Suppose that we have the following `Counter` React component implemented using the [react-redux](https://react-redux.js.org/) stack, and we want to write tests for it.

<iframe src="https://codesandbox.io/embed/redux-counter-388p5?autoresize=1&fontsize=14&hidenavigation=1&module=%2Fsrc%2FCounter.jsx&theme=dark&view=preview"
     style="width:100%; height:200px; border:0; border-radius: 4px; overflow:hidden;"
     title="redux-counter"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

#### Step 1: Identify the behaviours

The first step to find out what needs to be tested is to understand how the component behaves. The best way to do that is usually to interract with the component in a demo environment. To start out, look at the initial state, that is what is first being rendered. Once you know what the initial state is, you need to find every ways you can make this initial state change into a derived state. From each of those derived state, if the component behaves exactly the same way compared to the initial state, you have found all the behaviours. On the contrary, if from a derived state the component behaves differently, you have identified new behaviours that will also need to be tested.

After looking at what's initialy rendered and clicking on the available buttons, we can  assume that there are 5-6 behaviours depending on how we choose to split them. Don't forget that the initial state, is in itself a behaviour.

- It displays a count of zero at the start and an "edit" button
- When clicking on "edit" button
   - It replaces the "edit" button by "plus", "minus", and "save" buttons
   - It increments the count by one when clicking on the "plus" button
   - It decrements the count by one when clicking on the "minus" button
   - It replaces the "plus", "minus", and "save" buttons by "edit" button when clicking on the "save" button

#### Step 2: Write down all test cases titles

Spending time thinking about the test cases titles might be one of the most overlooked step in the testing world. However, doing so is crutial to establish a strong understanding of how the test cases should be split. Basically, the idea is to write the most detailed, succinct, and non-ambiguous description of each behaviours you have identified on step 1. Having them all written down before even thinking about the code needed to make the test work will help you understand how to split your tests. The scope of each of them will become much clearer in your head when comes the time to write those tests.

For our `Counter` example, I have basically already have done all the work on Step 1, but here's how it would look in the code:

```jsx
describe("Counter", () => {
  it('displays a count of zero at the start and an "edit" button', () => {
    // TODO
  });

  describe('when clicking on "edit" button', () => {
    it('replaces the "edit" button by "plus", "minus", and "save" buttons', () => {
      // TODO
    });

    it('increments the count by one when clicking on the "plus" button', () => {
      // TODO
    });

    it('decrements the count by one when clicking on the "minus" button', () => {
      // TODO
    });

    it('replaces the "plus", "minus", and "save" buttons by "edit" button when clicking on the "save" button', () => {
      // TODO
    });
  });
});
```

When dealing with derrived states, like the one we have after clicking on the *Edit* button, there are more than one valid solution to split the test cases. We could use only `it` blocs with longer titles or leverage `describe` blocs to shorten similar titles. In the end, it pretty much comes down to personal or team preferences. I personnaly try to minimise the usage of `describe` blocs, since it reduces readability of test case titles when you have a lot of them. You need to go back up the file to read the `describe` bloc to understand the full test.

> **Bonus tip**: get rid of the "should". In addition to having one less word to write (more succinct), it removes ambibuity and forces you to find a clearer phrasing. The UI _should_ not do _X_, it _does_ _X_. If it doesn't do X, it is not acceptable, the behaviour is broken and the test has to fail.

## How do we test

Now that we have successfully identified what we need to test, we can jump in the fun part: writing the actual tests. This step seems intimidating when you start out writing tests for a component that is new to you. Fortunately, no matter the UI component under test, all tests can follow roughly the same structure: AAA, which stands for "Arrange", "Act" and "Assert". A quick search on your favorite search engine will give you a detailed explanation of the pattern, but those words are pretty self-explanatory so I won't explain them here. Sometimes you can "Arrange" and loop "Act"-"Assert" if that makes your life easier. As explained previously, the line between unit, functional and other types of tests in a UI context is blurry and that's okay. As long as we stay productive and that our tests grant us confidence that the behaviours are working, we are happy.

Talking about confidence, _"The more your tests resemble the way your software is used, the more confidence they can give you."_. If there could only be one sentence you need to remember from this article, it would be this one. It is in fact the guiding principle of [Testing Library](https://testing-library.com/), the library we are using to test our UIs. In other words it means that our tests must only do stuff that a real user of the interface can do. Doing otherwise leads to tests that rely on implementation details. Tests that rely on implementation details will generates false negatives (behaviour did not change and test broke) and false positives (behaviour changed and test passed). Having false positives and false negatives reduce the confidence we have in our tests and generate additional maintenance costs. Less value plus higher cost equals lower return on investment and quite franckly a drop in happiness for everyone involved. Lets see how that principle applies to our counter example.

#### Step 3: Get the first test working

I like to start by testing the intial state, because it is usually the simplest test to perform. The intial state  is a behaviour in itself who's "Act" is simply the rendering process without any user interaction.

#### Step 4: Write the other tests

#### Step 5: Doubt your tests
