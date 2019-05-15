---
title: "Recursion"
date: 2019-05-13T07:42:58+07:00
draft: true
toc: false
images:
tags:
  - Dynamic Programming
  - Fibonacci
  - JavaScript
  - Memoization
  - Recursion
  - Tabulation
---

## What is recursion

The best one liner that I've come across to explain recursion is this:

> _"Recursion is when a function calls itself until it doesn't."_

> ~ mpj on [Fun, Fun, Function](https://www.youtube.com/watch?v=k7-N8R0-KY4)

## How to use recursion

One of the classic examples of illustrating recursion is by using it to determine the Nth value within the Fibonacci sequence.

### What is the Fibonacci Sequence

The Fibonacci sequence is a series of numbers, where each number is the sum of the two preceding numbers in the series.

Here's the first 12 numbers of the Fibonacci sequence:

```console
1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, 144
```

### A few things to note

1. Sometimes the Fibonacci sequence is depicted starting with 0, here we don't, but it really doesn't matter, as this article is about understanding recursion.
2. The first two values in the Fibonacci sequence are _always_ 1, and since you can't get the sum of the `two preceding numbers` of positions 1 and 2 in the sequence, these are considered given.
3. Take a look at 2 (in position 3), however, and you'll see that the two preceding numbers are 1 and 1. Add them together and you get two.
4. Same thing for 5 with 3 and 2, and so on...

### Contriving a recursive solution for Fibonacci

Let's start with a problem statement:

> Given a number, return the value from the Fibonacci sequence at that position.

With that in mind we can envision:

- receiving `1` and returning 1.
- receiving `2` and returning 1.
- receiving `3` and returning 2.
- receiving `4` and returning 3.
- receiving `5` and returning 5.
- etc...

### The anatomy of a recursive function

#### Base Cases

> **Remember:** Recursive functions are functions that call themselves until they don't.

Let's talk about "until they don't".

If we look at a while loop like the below, we can probably see that it will run forever:

```js
while (true) {
  console.log("Help, I'm in an infinite loop!");
}
```

There is nothing telling the above loop to stop what it's doing.

Now look at this example:

```js
let counter = 0;
while (counter < 10) {
  console.log(`Counter is: ${counter}`);
  counter++;
}
console.log("And we're done.");
return counter;
```

1. We enter the while loop when `counter` is less than 10.
2. We print a message.
3. We increment `counter`
4. When `counter` hits 10, we leave the while loop.
5. We print a final message.
6. We return `counter`

This is the basic premise behind, "until it doesn't"; however, when we are talking in terms of a recursive function, we use the phrase _base case_.

> A _base case_ tells a recursive function to stop calling itself (usually by returning something).

#### How a function calls itself

Take a look at this function:

```js
function fib(n) {
  console.log("We are in an infinite loop!");
  return fib(n);
}
```

While can see that the function actually returns itself, this is a really bad function for a couple key reasons.

1. There is no base case, telling the function not to call itself anymore.
2. The arguments in the returned function do not change.

Both of these problems cause the function to run in an infinite loop.

:point_up: **Remember this!**

1. You always need one or more base cases that will allow the function to stop calling itself.
2. When recursing back into a function, _the arguments must change_ in such a way that a base case will eventually be met, allowing the function to stop calling itself.

Let's fix these so that this recursive function is similar to the `while loop` example from above.

```js
function fib(n, counter = 0) {
  if (counter === 10) {
    console.log("Base case has been met.");
    return n;
  }
  console.log("We are in a recursive loop!");
  return fib(n, counter++);
}
```

While not terribly exciting, since we're always just returning `n`, we are at least not stuck in an infinite loop.

> :point_up: Note that the `counter` parameter is set with an initial value of 0, using [default parameters](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters), which is a new feature of JavaScript ECMAScript 2015 (a.k.a. ES6).

Why is works is due to the following:

1. The counter is set to 0.
2. If the counter is < 10, we call the fib function again _with the new argument_ of `counter++`.
3. We hit the base case once counter = 10, and we return `n`, which causes us to leave the function (i.e. "it doesn't call itself anymore").

So let's apply this pattern to the Fibonacci sequence.

## Solution

We know from the definition that the value of any given number, `n`, in the Fibonacci sequence is the sum of the two proceeding numbers.

So let's think about that for a moment:

To get the Fibonacci number at position `n`, we need to know the value of the Fibonacci numbers at positions `n-1` and `n-2`.

```console
value of Fibonacci at n = value of Fibonacci at n-1 + value of Fibonacci at n-2
```

:scream: If you feel your head imploding at this point, that's perfectly normal. Read the above line three times aloud and look at the below:

```js
function fib(n) {
  return fib(n - 1) + fib(n - 2);
}
```

We're getting there, but the above function is incorrect due to one key missing ingredient: there is no base case! This means, yep, an infinite loop.

But before we get to the base case, let's understand what the above function is actually trying to do.

1. It receives `n` as a value.
2. As we want to determine the Fibonacci number at position `n`, we need to know the Fibonacci numbers at positions `n-1` and `n-2`.
3. To do this, the function calls itself (twice) to determine what those values are.

### Okay, so how to we stop it

If we recall, the first two numbers in the Fibonacci sequence are _always_ 1. Therefore, we can write a base case around this.

```js
if (n <= 2) return 1;
```

With this: if `n` is `1` or `2`, the base case is met and the function will return `1`.

> :point_up: Take note that we are considering positions in the Fibonacci sequence. We are not considering positions in an array, so don't think as if we are trying to access values with a zero-based index.

### Putting it all together

```js
function fib(n) {
  if (n <= 2) return 1;
  return fib(n - 1) + fib(n - 2);
}
```

### What's happening here

Assuming we call the function with `fib(3)`, here's how the function works

1. It checks if `n` is <= 2, which it is not
2. It returns `fib(n - 1) + fib(n - 2)`, which is actually calling itself twice with different arguments, trying to get the two previous Fibonacci numbers in the sequence behind `n`.
3. Each of these are processed in turn.
4. As the function tries to resolve `fib(n - 1)`, the parameter `n` is now `2`, because fib was called with the argument `n - 1`, or `3 - 1`. :point_left: read that again
5. With `n = 2` the function will return 1, as the `base case` is satisfied.

:fire: At this point, we now know something!

```console
value of Fibonacci at 3 = :fire:1:fire: + value of Fibonacci at n-2
```

6. The function then proceeds to try and resolve `fib(n - 2)`
7. As the function tries to resolve `fib(n - 2)`, the parameter `n` is now `1`, because fib was called with the argument `n - 2`, or `3 - 2`. :bulb: Are things starting to click now?
8. With `n = 1` the function will return 1, as the `base case` is satisfied.

:fire: And now we know two things, and we have the solution!

```console
value of Fibonacci at 3 = :fire:1:fire: + :fire:1:fire: = 2
```

I used fib(3), as it's the shortest explanation; however, as we try to find Fibonacci numbers at higher positions of `n`, the process is the same but the `fib(n - 1) + fib(n - 2)`s will continue to stack up in memory as the function works its way toward the solution.

## Is Using Recursion a Good Idea

Using recursive functions to solve problems, while seemingly elegant, is often not the best approach.

The main reason for this is that complexity (both time and space) for recursive solutions is usually pretty bad. In fact, in can be horrible :japanese_ogre:

For example, the above solution would be considered to have a Big-O time complexity of O(2^n).

{{< figure src="/time-complexity.png" title="Big-O Time Complexity" >}}

### Why is it horrible

Every time the function runs this line of code:

```js
return fib(n - 1) + fib(n - 2);
```

Memory is allocated (on the computer's [call stack](https://developer.mozilla.org/en-US/docs/Glossary/Call_stack)) toward resolving any `fib(n)` where `n` is greater than 2 (more on "greater than two" below).

The biggest potential problem with this is that there is a finite number of things that you can add to the _call stack_, and if you pass a large enough number as `n`, you will exceed the limit of the _call stack_ resulting in a _stack overflow_, which basically crashes the program.

The other problem with this approach is that it is inefficient, in that every time the that return line runs, we have to calculate it, often recalculating the same thing more than once.

For example, take a look at the following representation of how `fib(6)` is derived:

{{< figure src="/fib-tree.png" title="fib(6)" >}}

Look how many times F(3), or fib(3), is called: 3 times.

> :point_up: Note that fib(1) and fib(2) are not of great concern, because we have basically hard coded the result of these into the function; however, when seeking ever larger fib(n)'s greater than 2, the repeated calculations become significant.

To address this, we can leverage the concept of [Dynamic Programming]({{<ref "/posts/dynamicProgramming">}}) toward making this function much more efficient.

## Dynamic Programming Approaches

### Memoization

```js
function fib(n, memo = []) {
  if (memo[n]) return memo[n];
  if (n <= 2) return 1;
  let res = fib(n - 1, memo) + fib(n - 2, memo);
  memo[n] = res;
  return res;
}
```

### Tabulation

```js
function fib(n) {
  const fibNumbers = [0, 1, 1];
  if (n <= 2) return 1;
  for (let i = 3; i <= n; i++) {
    fibNumbers[i] = fibNumbers[i - 1] + fibNumbers[i - 2];
  }
  return fibNumbers;
}
```
