# Higher-Order Functions and Callbacks

## Learning Goals

- One
- Two
- Three

## Introduction

We've learned that functions in JavaScript are considered first-class, which
opens up the ways we can utilize them. We can assign them as values to a
variable, pass them as arguments to another function, and return them from
another function. These abilities allow some functions to be considered of
higher-order.

In this lesson we will discuss what higher-order functions are and dive deeper
into the intricacies they present.

## Higher-Order Functions

In learning about what JavaScript functions can do thanks to being first-class,
we've already seen examples of some higher-order functions. **A function is
considered higher-order if it either accepts functions as an argument, or
returns one (or both!).**

In this example we've seen previously:

```js
function cashier(sayGreeting) {
  sayGreeting();
}
```

`cashier()` is a higher-order function because it _accepts a function as an
argument_.

In this example we've seen previously:

```js
function returnBook(book) {
  console.log("Starting return of: ", book);
  console.log("Checking receipt...");
  console.log("Checking condition...");

  return function () {
    console.log("Refund processed!");
  };
}
```

`returnBook` is a higher-order function because it _returns a function_.

While both types of higher-order functions are powerful, the type you will
likely encounter and use the most is `cashier()`. Well, not `cashier()`
specifically, but a function that accepts functions as arguments. They're so
useful that a function getting passed in as an argument also has a name -
callback function.

> **Note**: As we dive deeper into higher-order functions, it bears repeating:
> this is **very** complicated material! This is likely the first time you're
> encountering any of this stuff so, if you're struggling with the new concepts,
> don't sweat it! Set aside some extra time to re-read and practice, and make
> sure you're coding along with every example we cover in these lessons.

## Callback Functions

**When you pass in a function as an argument, that passed in function is
considered a callback.** The term derives from the fact that the function isn't
invoked immediately — instead it's _called back_, or invoked at a later point.

Let's look at our `cashier()` example again, and bring back one of the greeting
functions as well:

```js
function welcome() {
  console.log("Welcome to Bookish Mart!");
}

function cashier(sayGreeting) {
  sayGreeting();
}

cashier(welcome); // => Welcome to Bookish Mart!
```

As we already established, `cashier()` is considered a higher-order function. When
we invoke it at the bottom and pass in `welcome`, `welcome` is now considered a
callback function.

Here we're passing in a _reference_ to a declared function, but we can also
directly pass in _anonymous functions_. Take for example:

```js
function cashier(sayGreeting) {
  sayGreeting();
}

cashier(() => console.log("Thanks for shopping at Bookish Mart!"));
// => Thanks for shopping at Bookish Mart!
```

Let's break this down to clarify what's going on:

1. We passed an anonymous function, `() => console.log("Thanks for shopping at
Bookish Mart!")` to `cashier()`.
   - Confused with this single-line syntax? Recall what we learned about arrow
     functions - they can be written in one line when there is only one
     expression within the function body.
2. `cashier()` stored the passed-in anonymous function, i.e. the _callback
   function_, in the local `sayGreeting` variable.
3. The `sayGreeting` variable was invoked inside of the `cashier()` function
   body, which in turn invokes the stored callback function.
4. The invoked callback runs what it was programmed to do: log a string to the
   console.

Another thing of note: the anonymous **callback function was not invoked when
passed in as an argument**.

After all, we didn't want to invoke the callback function when we passed it in.
We only wanted it to run when told to _inside_ the higher-order function's body.
We did so by invoking the parameter variable the callback was saved to, which
was `sayGreeting` in this case.

> **Note**: We used an anonymous arrow function in that example, but we can
> also use the `function` keyword and get the same result:
>
> ```js
> cashier(function () {
>   console.log("Thanks for shopping at Bookish Mart!");
> });
> // => Thanks for shopping at Bookish Mart!
> ```
>
> Either syntax works just fine! Arrows just tend to make it cleaner and easier
> to read.

## Passing Arguments to Callbacks

As we just saw, callback functions should never be immediately invoked when
passing them in. Doing so will make them run immediately, even if we never
invoke them inside the function...

```js
function welcome() {
  console.log("Welcome to Bookish Mart!");
}

function cashier(sayGreeting) {
  console.log("I refuse to say a greeting!");
}

cashier(welcome());
// => Welcome to Bookish Mart!
// => I refuse to say a greeting!
```

... or worse - break the function you're passing it into entirely.

```js
function welcome() {
  console.log("Welcome to Bookish Mart!");
}

function cashier(sayGreeting) {
  sayGreeting();
}

cashier(welcome()); // => TypeError: sayGreeting is not a function
```

In this scenario, `sayGreeting` is no longer a reference to `welcome()`. It is a
reference to the _result_ (i.e. _return_ value) of `welcome()` - which is not a
function.

So, what happens if a callback accepts arguments? The only way to pass an
argument to a function is to invoke it. If the needed argument is available
within the higher-order function, then there's no problem at all:

```js
function welcome(storeName) {
  console.log("Welcome to", storeName);
}

function cashier(sayGreeting) {
  const store = "Bookish Mart";
  sayGreeting(store);
}

cashier(welcome);
// => Welcome to Bookish Mart
```

We pass the callback as normal, then when invoking it via the parameter
(`sayGreeting`) inside the higher-order function (`cashier()`), we can pass in
the argument (`store`) there.

If the needed argument is _not_ available inside, then we have a conundrum on
our hands. As we said, the only way to pass an argument to a function is to
invoke it - but we can't do that here. Instead, we first have to pass the
callback's required arguments _alongside_ it to the higher-order function. Let's
see that in action:

```js
function welcome(name) {
  console.log("Welcome to Bookish Mart,", name);
}

function cashier(sayGreeting, name) {
  sayGreeting(name);
}

cashier(welcome, "Alejandra");
// => Welcome to Bookish Mart, Alejandra
```

Now, the `welcome()` function gets saved to the parameter `sayGreeting` again.
Additionally, we've added a new parameter called `name`, which saves the second
argument we passed in, `Alejandra`. Inside the function body, we invoke
`sayGreeting` and pass it the `name` parameter.

With the values we passed to those parameters, `sayGreeting(name)` essentially
acts as if we called `welcome("Alejandra")`.

Whether you have one, two, three, or more arguments to pass to a callback
function, you can follow the same process. Access them from within the
higher-order function, or add the needed parameters to the higher-order function
and pass the arguments _alongside_ the callback.

## Conclusion

You might wonder why even use callbacks, why not just directly call the function
within the higher-order function instead? For example:

```js
function welcome(name) {
  console.log("Welcome to Bookish Mart,", name);
}

function cashier() {
  welcome("Alejandra");
}

cashier(); // => Welcome to Bookish Mart, Alejandra
```

This provides the same exact output as when we used callbacks. In fact, it even
looks a bit cleaner in this case. Well, what if we want our cashier to say a
goodbye greeting instead? The way we currently have it written, `cashier()` can
only ever say welcome. It is specifically reliant on `welcome()`, a function
that is outside of itself, rather than being adaptable.

Using callbacks instead makes the higher-order function more general, more
variable. It does not have to rely on anything outside of its own scope, just
arguments passed to it and whatever it's been programmed to do within its own
body.

Higher-order functions are powerful tools that JavaScript allows us to use, take
advantage of them! They are not always necessary to use, of course. Still, with
higher-order functions now under our belt, we can at least start to program
while keeping abstraction in mind.

## Resources

- [Eloquent JS: Higher-Order
  Functions](https://eloquentjavascript.net/05_higher_order.html)
- [JavaScript | Callbacks](https://www.geeksforgeeks.org/javascript-callbacks/)
