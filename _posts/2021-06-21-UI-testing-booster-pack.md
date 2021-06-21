---
layout: post

title: "The UI testing booster pack"

tags: [UI, tests, react, testing library, frontend]

author:
  name: Gaël Dostie
  bio: Software Developer, Administration Console UI team
  image: gdostie.jpeg
---

You feel like you do a decent job at creating new user intefaces or modifying existing ones, but writing tests to cover your changes takes you more time than to write the actual source code. You have experience writing tests for clear cut units of code like functions or classes, but it seems that testing UI is just too different. Having lost all intuition about what needs to be tested, what needs to be mocked, how to split your test cases or even how to write a simple assertion, you feel lost and unproductive. If you can relate to any of the above, this article is for you.

This post aims to provide a UI testing "booster pack", all batteries included, that will get you rolling in no time. Having the right mindset and using the right tools will make you almost look forward to writing tests for UIs. Not only because your tests will grant you confidence and improve the quality of the code you produce, but also quite frankly because it will become an enjoyable thing to do.

<!-- more -->

## Why do we write tests

Lets start with _why_. If you think about it, when we create or change something visual, we surely look at it and interact with it manualy while changing its source code until we are satisfied with the result. So why do we even bother creating an additional scripted test that doesn't have eyes to tell us if something looks good or bad?

A simple answer to that question is that scripted tests (unit, functional, end-to-end, all of them, we'll get to that part later) provide us with a level of confidence that the UI (still) works as intended.

> In a sense, scripted tests are a written garantee that the UI under test respects a given set of behaviours.

Its important to always keep that in mind while testing, because it helps us define what needs to be tested. Which brings us to my next point.

## What do we need to test

There are already a handfull of good articles out there about the differents levels of tests that exist: static, unit, integration, functional, snapshots, end-to-end, manual, mutants, performance, etc. Those articles usually offer good recommendations about the amount of each of them you should aim to have based on your needs. However, in the universe of UI tests, the line between some of those categories can become pretty blurry. What is a unit? Is a button a unit? What if the screen shows a form with a submit button, is that a unit or a functionality?

The best way to make sense out of all that confusion and remain productive is to not waste energy on putting a label on the kind of tests we're doing and just remember why we're writing tests. We want to provide a garantee about certain behaviours. Simply put, we test behaviours. Whether the behaviour is a unit or a functionality, we don't really mind, we only care that it works. If the UI under test offers no noticeable behaviours from a user standpoint (and this is really the key), then we don't need nor want to write a test for that specific UI.

Suppose that we have the following `Counter` React component implemented using the [react-redux](https://react-redux.js.org/) stack, and we want to write tests for it.

<iframe src="https://codesandbox.io/embed/redux-counter-388p5?autoresize=1&fontsize=14&hidenavigation=1&module=%2Fsrc%2FCounter.jsx&theme=dark&view=preview"
     style="width:100%; height:200px; border:0; border-radius: 4px; overflow:hidden;"
     title="redux-counter"
     sandbox="allow-forms allow-modals allow-popups allow-presentation allow-same-origin allow-scripts"
   ></iframe>

#### Step 1: Identify the behaviours

The first step to find out what needs to be tested is to understand how the component behaves. The best way to do that is usually to interract with the component in a demo environment. To start out, look at the initial state, that is what is first being rendered. Once you know what the initial state is, you need to find every ways you can make this initial state change into a derived state. From each of those derived state, if the component behaves exactly the same way compared to the initial state, you have found all the behaviours. On the contrary, if from a derived state the component behaves differently, you have identified new behaviours that will also need to be tested.

After looking at what's initialy rendered and clicking on the available buttons, we can assume that there are 5-6 behaviours depending on how we choose to split them. Don't forget that the initial state, is in itself a behaviour.

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

When dealing with derrived states, like the one we have after clicking on the _Edit_ button, there are more than one valid solution to split the test cases. We could use only `it` blocs with longer titles or leverage `describe` blocs to shorten similar titles. In the end, it pretty much comes down to personal or team preferences. I personnaly try to minimise the usage of `describe` blocs, since it reduces readability of test case titles when you have a lot of them. You need to go back up the file to read the `describe` bloc to understand the full test.

> **Bonus tip**: get rid of the "should". In addition to having one less word to write (more succinct), it removes ambibuity and forces you to find a clearer phrasing. The UI _should_ not do _X_, it _does_ _X_. If it doesn't do X, it is not acceptable, the behaviour is broken and the test has to fail.

## How do we test

Now that we have successfully identified what we need to test, we can jump in the fun part: writing the actual tests. This step seems intimidating when you start out writing tests for a component that is new to you. Fortunately, no matter the UI component under test, all tests can follow roughly the same structure: AAA, which stands for "Arrange", "Act" and "Assert". A quick search on your favorite search engine will give you a detailed explanation of the pattern, but those words are pretty self-explanatory so I won't explain them here. Sometimes you can "Arrange" and loop "Act"-"Assert" if that makes your life easier. As explained previously, the line between unit, functional and other types of tests in a UI context is blurry and that's okay. As long as we stay productive and that our tests grant us confidence that the behaviours are working, we are happy.

Talking about confidence, _"The more your tests resemble the way your software is used, the more confidence they can give you."_. It is the guiding principle of [Testing Library](https://testing-library.com/), the library we are using to test our UIs. In other words it means that our tests must only do stuff that a real user of the interface can do. Doing otherwise leads to tests that rely on implementation details. Tests that rely on implementation details will generates false negatives (behaviour did not change and test broke) and false positives (behaviour changed and test passed). Having false positives and false negatives reduce the confidence we have in our tests and generate additional maintenance costs. Less value plus higher cost equals lower return on investment and quite franckly a drop in happiness for everyone involved. Lets see how that principle applies to our counter example.

#### Step 3: Get the first test working

I like to start by testing the intial state, because it is usually the simplest test to perform. The intial state is a behaviour in itself who's "Act" is simply the rendering process without any user interaction.

The quickest way to know what the minimal setup is to get the test running is simply to render the component and see what happens. I also usually use the [`screen.logTestingPlaygroundURL`](https://testing-library.com/docs/queries/about/#screenlogtestingplaygroundurl) method to understand what is being rendered so that I can have a general idea of what can be asserted from there.

```tsx
it('displays a count of zero at the start and an "edit" button', () => {
  render(<Counter />);

  screen.logTestingPlaygroundURL();
});
```

Running the test above gives us the following error.

> Could not find "store" in the context of "Connect(Counter)". Either wrap the root component in a <Provider>, or pass a custom React context provider to <Provider> and the corresponding React context consumer to Connect(Counter) in connect options.

Without even looking at the component's implementation, it is pretty obvious from the error message that we are missing something: a redux store provider. Looking at the component's implementation as little as possible forces us to not rely on implementation details, which is really the key to better tests as explained earlier.

```tsx
it('displays a count of zero at the start and an "edit" button', () => {
  const store = createStore(combineReducers({ counter: CounterReducer }));
  render(
    <Provider store={store}>
      <Counter />
    </Provider>
  );

  screen.logTestingPlaygroundURL();
});
```

Now the test passes and gives us a nice [playground link](https://testing-playground.com/#markup=DwEwlgbgfMAOUBUAWBTABAYwPYFcB2ALmmAM4BcaADMAPTzABGOBBWeUAouAbUy2zBrhoQ).

The only thing left to do is to assert that the user see's on the screen what he is supposed to see. This part is pretty straightforward if we use the testing playground, follow the [best practices](https://testing-library.com/docs/queries/about#priority) and avoid the [common mistakes](https://kentcdodds.com/blog/common-mistakes-with-react-testing-library).

```tsx
it('displays a count of zero at the start and an "edit" button', () => {
  const store = createStore(combineReducers({ counter: CounterReducer }));
  render(
    <Provider store={store}>
      <Counter />
    </Provider>
  );

  expect(screen.getByText("The count is: 0")).toBeInTheDocument();
  expect(screen.getByRole("button", { name: /edit/i })).toBeInTheDocument();
});
```

With that, our first test is done and we can move on to the other more complicated test cases.

#### Step 4: Finish the remaining test cases

According to the test plan we put down on step 2, the next test in line is this one

```tsx
describe('when clicking on "edit" button', () => {
  it('replaces the "edit" button by "plus", "minus", and "save" buttons', () => {
    // TODO
  });
});
```

We can copy-paste the setup portion from the previous test. We already know how to get the edit button since we did this in the previous test, so from there its not much more work to trigger a click on the same element using [`user-event`](https://testing-library.com/docs/ecosystem-user-event/). At this point we reach unknown territories, but we can apply the same strategy as before involving the testing playground.

```tsx
it('replaces the "edit" button by "plus", "minus", and "save" buttons', () => {
  const store = createStore(combineReducers({ counter: CounterReducer }));
  render(
    <Provider store={store}>
      <Counter />
    </Provider>
  );

  const editButton = screen.getByRole("button", { name: /edit/i });
  userEvent.click(editButton);

  screen.logTestingPlaygroundURL();
});
```

Now the screen looks like [this](https://testing-playground.com/#markup=DwEwlgbgfMAOUBUAWBTABAYwPYFcB2ALmmAM4BcaADMAPTzABGOBBWeUA1LUy2zD63YBabs0H8xfAMoBDCClG92tcNCA), and we end up with a test that is similar to the first one but verifies a different behaviour.

```tsx
it('replaces the "edit" button by "plus", "minus", and "save" buttons', () => {
  // Arrange
  const store = createStore(combineReducers({ counter: CounterReducer }));
  render(
    <Provider store={store}>
      <Counter />
    </Provider>
  );

  // Act
  const editButton = screen.getByRole("button", { name: /edit/i });
  userEvent.click(editButton);

  // Assert
  expect(screen.getByText("The count is: 0")).toBeInTheDocument();
  expect(
    screen.queryByRole("button", { name: /edit/i })
  ).not.toBeInTheDocument();
  expect(screen.getByRole("button", { name: "+" })).toBeInTheDocument();
  expect(screen.getByRole("button", { name: "-" })).toBeInTheDocument();
  expect(screen.getByRole("button", { name: /save/i })).toBeInTheDocument();
});
```

The thinking process to wrap up the other test cases is the same. We can extract the repeating setup portion into a `beforeEach` block if we want, but it is not mandatory and not always a good practice.

> Unlike what we've been accustomed to do in source code, copy-pasting blocks of code in each tests is totally fine for the sake of readability and test independance. [Here](https://mtlynch.io/good-developers-bad-tests/) is a great read on that subject that I highly recommend.

#### Step 5: Doubt your tests

Once we're all done writting the test cases we have envisionned at the start, we could stop there and call it a day. Truthfully that would be totally acceptable. In an context where one must rush out a feature, just testing the happy path with the most straightforward assertions is a whole lot better than no tests at all. However, when developping a critical component that needs to stand the test of time, going just one step further will future proof your work against refactors and regressions. To be honnest, at the beginning this step can mean added time to delivery, but with experience you will start writing better code and better tests right from the start.

The idea is to refine the expected behaviours by asking yourself "what if" questions.

Let's say we wrote the following test for the "+" button behaviour:

```tsx
it('increments the count by one when clicking on the "plus" button', () => {
  const store = createStore(combineReducers({ counter: CounterReducer }));
  render(
    <Provider store={store}>
      <Counter />
    </Provider>
  );

  userEvent.click(screen.getByRole("button", { name: /edit/i }));
  userEvent.click(screen.getByRole("button", { name: "+" }));

  expect(store.getState().counter.count).toBe(1);
});
```

The test passes just fine and does validate that the count has been incremented by one. Now, what if someone refactors the component to remove redux altogether while retaining all the functionalities? There wouldn't be any `store` object to expect from anymore. The test would fail while the behaviour hasn't changed creating a false negative. The problem here is that our test relies on an implementation detail: redux. Would our user see if the count has changed by looking into the state object himself? Of course not, the user only looks at the screen.

```ts
expect(screen.getByText("The count is: 1")).toBeInTheDocument();
```

The above assertion is a lot stronger since it validates what the user really sees. We could refactor the component to use any library and the test would still pass, as long as the functionnality is preserved. Yes the `<Provider>` would probably have to be removed, but it wouldn't hurt the test's logic to leave it there as well.

Pushing the doubt further: what if a new intern (I always imagine an intern in those scenarios 😄) breaks the Counter's logic so that when pressing on the "+" button, it doesn't increment more than once anymore. So from 0 it goes to 1, then stays at 1 when clicking on "+" again. Our current test wouldn't pick up on that breakage of functionality, leading to a false positive.

The solution here is to add anoter "Act-Assert" loop. If we continue from the Counter version free of redux it gives us a test looking like this:

```tsx
it('increments the count by one when clicking on the "plus" button', () => {
  render(<Counter />);

  userEvent.click(screen.getByRole("button", { name: /edit/i }));

  const plusButton = screen.getByRole("button", { name: "+" });

  userEvent.click(plusButton);
  expect(screen.getByText("The count is: 1")).toBeInTheDocument();

  userEvent.click(plusButton);
  expect(screen.getByText("The count is: 2")).toBeInTheDocument();
});
```

## Bonus tips for the road

If you have made it this far congratulations! You are ready to roll on the UI testing highway to no regression land. This section regroups tips and tricks in random order that I noticed helped me write stronger tests.

#### Prefer hardcoded expectations

Sometimes we have components that leverage util functions to render the right thing to the screen.

```jsx
const SaveButton = () => <button onClick={save()}>{translate("Save")}</button>;
```

It can be tempting to reuse the same util in your tests assertions, thinking that if someone changes the string it won't break our test.

```jsx
expect(
  screen.getByRole("button", { name: translate("Save") })
).toBeInTheDocument();
```

However by doing so, you are more subject false positives. Imagine someone (probably an intern again) mistakenly changes the "Save" translation for "Cancel". I know this is a bit extreme, but it is just to illustrate my point. In that case the test would still pass, because `translate` would return the same wrong string both in the component and the test. The UI would then save when clicking on "Cancel" which you would guess is not what we want!

> A visible string that changes, is a break in functionality.

```jsx
expect(screen.getByRole("button", { name: /'save'/i })).toBeInTheDocument();
```

Hardcoding the "save" string directly into the test protects us from that potential breakage. The same principle applies for URLs, numbers, dates and other visible values you could think of.

#### Mock as fewer things as possible

Mocking everything that is not part of the component under test is a very natural thing to do. We assume those external dependencies (external to the component under test) have tests of their own, so it wouldn't be a good practice to do the same tests again. This assumption is true to an extent, but mocking something ultimately means your test relies on an implementation detail. Remember Testing Library's guiding principle?

> The more your tests resemble the way your software is used, the more confidence they can give you.

Each thing you mock makes your test resemble less the way your software is used, which drives down the value of the test.

Let's say we have the following component and we want to validate that a message is displayed when the entered email address is invalid.

```jsx
import {isValidEmail} from './validators';

const MyComponent = () => {
  // other stuff
  const validationMessage = isValidEmail(inputValue) ? '' : 'Invalid email';

  return (
    // other stuff
    {validationMessage ? <span className='invalid'>{validationMessage}</span> : null}
  );
};
```

We could mock the `isValidEmail` util function thinking _"the util is tested somewhere else, I should not test that again"_. We could end up with a test looking like this:

```jsx
import { isValidEmail } from "./validators";

jest.mock("./validators");

it("displays an invalid message when the email is invalid", () => {
  isValidEmail.mockReturnValue(false);
  render(<MyComponent />);
  expect(screen.getByText('Invalid email')).toBeInTheDocument();
});
```

It's better than no tests at all, but at least two things make this a weak test:
1. It doesn't validate that `isValidEmail` is called with the input value (potential false positive)
2. If the component is refactored to use a different validator function (for example an external lib), we will get a false negative

Sure we could fix the first problem by asserting the mock is called with the input value we type in, but when using the mock strategy, it doesn't force you to think about adding these additional assertions so they become really easy to forget. You also would still have problem number 2.

The solution to both problems is to make your test ressemble the way a user would use the interface and drop the mock.

```jsx
it("displays an invalid message when the email is invalid", () => {
  render(<MyComponent />);
  const emailInput = screen.getByRole('textbox', {name: 'email'});
  userEvent.type(emailInput, 'invalid email address');
  expect(screen.getByText('Invalid email')).toBeInTheDocument();
});
```

Don't get me wrong, testing the `isValidEmail` util separately is still a very good idea. It gives you confidence that all the edge cases are covered and that wherever the util is used, it will work as it is expected to. In the components that use the util we could only test the most common branches. For example one test for a valid email and one test for an invalid email. After all we only care about if the validation message is displayed when the right conditions occur, so that would be enough.

If you like that idea of mocking fewer things as possible, it is even possible to stop mocking the API client entirely. I won't go into details about this one since we have yet to try this in our team. If that interests you, [here](https://kentcdodds.com/blog/stop-mocking-fetch)'s a great article about it.

## Closing remarks

Writing tests for UIs is like doing fine arts. Tests come in many colours, lines can get pretty abstract and every artist has his own style. This article's purpose was to help you conquer your blank page syndrome rather than telling you which colour is the best.

If there is one sentence you need to remember from this post, it would be [Testing Library](https://testing-library.com/)'s guiding principle: _"The more your tests resemble the way your software is used, the more confidence they can give you"_.


