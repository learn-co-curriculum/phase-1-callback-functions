# Callback Functions

## Learning Goals

1. Define callback functions
2. Practice using callback functions

## Introduction

When I was your age, I had a boat.

![Boat](https://user-images.githubusercontent.com/17556281/28758193-16bc31b8-7562-11e7-8539-cc39b31def8f.jpeg)

It was not a fancy boat or a particularly nice boat, but it was my boat.

Boats, as you know, have many moving parts. And these parts need to be cleaned.

Now, I could have cleaned every part separately, _iterating_ through the piles
of barnacle-encrusted tools one by one, preparing to clean each individual piece
with a bit of a care.

Or I could get a bit smarter about it. I could prepare once for each group of
related items, so that I wouldn't have to prepare to clean each item
individually. In code, that might look like:

``` javascript
function clean(item) {
  console.log(`I just cleaned a ${item}`);
}

const nails = ["rusty nail", "rusty nail", "bent nail", "clean nail"];

for (let i = 0; i < nails.length; i++) {
  clean(nails[i]);
}

const planks = ["splintered plank", "straight plank", "bent plank"];

for (let i = 0; i < planks.length; i++) {
  clean(planks[i]);
}

console.log('done!');
```

And so on and so forth. Well now, this wasn't so bad, but I noticed that I was
preparing to clean everything — and cleaning everything, really — in the same
way. What if I could just group all of the things that I needed to clean, and
prep for the cleaning once?

## Define Callback Functions

In JavaScript, we have the ability to pass functions as arguments. This makes it
really easy to abstract functionality out of our programs, making the programs
easier to reason about by breaking them into smaller chunks. Take a look at the
following refactor of the code for our cleaning routine:

``` javascript
function groupAndClean(items, cleaningMethod, done) {
  for (let i = 0; i < items.length; i++) {
    cleaningMethod(items[i]);
  }

  done();
}

groupAndClean(nails.concat(planks), clean, function() {
  console.log('Whew, that was a lot of work!');
})
```

The `groupAndClean()` function has three parameters: the first references an
array containing the items to be cleaned and the other two hold **functions**.
When we call `groupAndClean()`, we pass the items to be cleaned (combined into a
single array using JavaScript's [`Array` `.concat` method][concat]), a
_reference_ to the `clean()` function we defined earlier, and an _inline_
function containing the code we want to execute after all the items have been
cleaned.

Functions that are passed as arguments to other functions are commonly referred
to as `callback functions`. That's because they're _called back_ within the body
of the function they're passed to. Callbacks are frequently used for
asynchronous operations, like requesting a JSON file from a server, or in the
case of Node.js, accessing the file system, a database, etc.

## Practice Using Callback Functions

Let's look at another example. Say we have a function `doTo5()` that lets us
do anything we want to the number 5 (follow along in the browser's console!):

``` javascript
function doTo5(anythingFunction) {
  return anythingFunction(5);
}
```

Well, let's try it out. Can we divide with it?

``` javascript
function divide10ByN(n) {
  return 10 / n;
}

doTo5(divide10ByN); //=> 2
```

Cool, cool. Can we append it to a string?

``` javascript
function appendToHello(s) {
  return `Hello, ${s}!`;
}

doTo5(appendToHello); //=> 'Hello, 5!'
```

Some things to note:

- neither `divide10ByN()` nor `appendToHello()` knows that it's going to receive
  `5` specifically — these functions only know that they're going to receive _an
  argument_. They're very generic.

- We do not _call_ `divide10ByN` or `appendToHello` when we pass them as
  functions to `doTo5()`. Instead we are passing them by _reference_ — that is,
  we pass just their name without any parentheses after it. That's because the
  calling actually happens _inside_ `doTo5()`, when `doTo5()` _calls back_ to
  the "outside world."

### A Final Example

Say we have a very expensive operation we need to execute. We can use callbacks
to help us encapsulate that operation into its own function by setting it up to
take a callback function as an argument. That function gets called after the
operation is completed (similarly to how `done()` was used in the boat example):

``` javascript
function somethingExpensive(callback) {
  // do something crazy,
  // like fetching a bajillion websites
  // then pass their data to callback:
  callback(data);
}
```

You can imagine that we might need to do different things with the data in
different parts of our program. This approach allows us to separate execution of
the expensive operation from the functions that use the data it returns: once
the operation is complete, we simply pass the data as an argument to whichever
function we need.

## Resources

- [JavaScript Is Sexy: Callbacks][JIS: Callbacks]
- [StackOverflow: Explain Callbacks in Plain English][SO: Callbacks]

[JIS: Callbacks]: http://javascriptissexy.com/understand-javascript-callback-functions-and-use-them/
[SO: Callbacks]: http://stackoverflow.com/questions/9596276/how-to-explain-callbacks-in-plain-english-how-are-they-different-from-calling-o
[concat]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/concat
