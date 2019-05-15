---
title: "Max Array Sum Non-Adjacent"
date: 2019-05-13T07:42:58+07:00
toc: false
images:
tags:
  - JavaScript
  - Max Array Sum
  - Non-adjacent
  - Kadane's Algorithm
---

## The Problem

Given an array of integers, find the subset of non-adjacent elements with the maximum sum and calculate the sum of that subset.

For example, given an array `arr = [-2, 1, 3, -4, 5]` we have the following possible subsets:

| Subset     | Sum |
| ---------- | --- |
| [-2, 3, 5] | 6   |
| [-2, 3]    | 1   |
| [-2, -4]   | -6  |
| [-2, 5]    | 3   |
| [1, -4]    | -3  |
| [1, 5]     | 6   |
| [3, 5]     | 8   |

## Some Background

On the surface, this might seem like a simple problem that you could jump in and start coding on. Go ahead and Google ["max array sum non-adjacent"](https://www.google.com/search?rlz=1C1CHBF_enTH824TH824&ei=YVDZXK-RDIv6vgS_goP4Aw&q=max+array+sum+non+adjacent) and give it a whirl, if you're game. I did and found enough Techsarcana that I felt compelled to break things down here due to a lot of unexplained depth that should be addressed before attempting this solution naively.

First, this a variation on the `Maximum Sub-array problem`, in that we are asked to work with non-adjacent entries in an array. Frankly, `Max Sub-array` is hard enough to tackle on its own (with adjacent entries), if you've never come across this type of problem before. And to be asked to perform the variant should be considered cruel and unusual punishment.

There is a well-known solution for `Max Sub-array`, in fact, and it was proposed decades ago by a man named [Joseph Born Kadane](https://en.wikipedia.org/wiki/Joseph_Born_Kadane). Kadane earned an A.B. in mathematics from Harvard College and a Ph.D. in statistics from Stanford, so don't feel too bad if you didn't conjure your own solution straight out of the gates.

### Kadane's Algorithm

```javascript
function maxSumConsecutive(arr) {
  for (let i = 1; i < arr.length; i++) {
    if (arr[i - 1] > 0) {
      arr[i] += arr[i - 1];
    }
  }
  return arr;
}
```

Kadane's algorithm performs the following steps:

1. It loops through an array, starting at the second position of the array (`i=1`) through to the end.
2. It looks at the value of the index prior to the current index in the loop (`arr[i-1]`) and checks if that value is greater than 0.
3. If `arr[i-1]` is greater than zero, it will overwrite the current index value with the sum of the current index value and the prior index value.
4. It returns the altered array, whereby each index in the array (starting at `arr[1]`) is the `Max Sum` up to that position in the array (left to right).

The result is that the highest value in the returned array is the `Max Sum` of the array.

Running the algorithm on the array of `[-2, 1, 3, -4, 5]` (from above) results in the following:

```console
[ -2, 1, 4, 0, 5 ]
```

Here, 5 is the `Max Sum` of the array, based on the **contiguous** subset of `[1, 3, -4, 5]` or just `[5]`, really. I know, not that exciting, but other arrays return similar (potentially more interesting) results. For example:

Input:

```console
[-2, 1, -3, 4, -1, 2, 1, -5, 4]
```

Output:

```console
[ -2, 1, -2, 4, 3, 5, 6, 1, 5 ]
```

Here, 6 is the `Max Sum` of the array, based on the **contiguous** subset of `[6, -2, -3, 1, 5]`.

Thank you, Kadane! :tada:

> :point_up: We've got a ways to go. Remember that we haven't even touched on the idea of non-adjacent values yet. But at least now, we have a basis for deriving the `Max Sum` of a contiguous subset of an array.

### Alternative function structure

Another way to write Kadane's algorithm is like this:

```js
function maxSumConsecutive(arr) {
  let maxCurrent = arr[0];
  let maxGlobal = arr[0];

  for (let i = 1; i < arr.length; i++) {
    if (arr[i] > maxCurrent + arr[i]) {
      maxCurrent = arr[i];
    } else {
      maxCurrent = arr[i] + maxCurrent;
    }
    if (maxCurrent > maxGlobal) {
      maxGlobal = maxCurrent;
    }
  }
  return maxGlobal;
}
```

This function performs the following actions:

1. It assigns the first value in the array to two variables: `maxCurrent` and `maxGlobal`.
2. It loops through array, starting at the second position of the array (`i=1`) through to the end.
3. On each pass through the loop:
   - It compares the value of `arr[i]` with the sum of the values `arr[1]` and `maxCurrent` and assigns the larger of the two values to `maxCurrent`.
   - It compares the values of `maxCurrent` and `maxGlobal` and assigns the value of `maxCurrent` to maxGlobal if `maxCurrent` is larger.
4. It returns the value of `maxGlobal` after looping through all values in the array.

While the overall functionality of the algorithm has not changed, this structure--with the introduction of variables--gets us one step closer to being able to handle the non-adjacent part of the originally stated problem.

### Using the Math.max() function

The `if statements` in the above are kind of clunky, so lets use `Math.max()` to clean things up a bit:

```js
function maxSumConsecutive(arr) {
  let maxCurrent = arr[0];
  let maxGlobal = arr[0];

  for (let i = 1; i < arr.length; i++) {
    maxCurrent = Math.max(arr[i], maxCurrent + arr[i]);
    maxGlobal = Math.max(maxGlobal, maxCurrent);
  }
  return maxGlobal;
}
```

> :point_up: [Math.max()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/max) is a handy, built-in JavaScript function that returns the largest value from a comma-separated list. Other languages have a built-in version of `Math.max()`, so use the one that's appropriate for you.

:dizzy: I found this video to be one of the best in explaining Kadane's algorithm. If your head is spinning at this point, try watching this video it few times. It may help.

{{< youtube 86CQq3pKSUw >}}

I've intentionally used the same variable names (albeit with camelCase) and structure from the video examples, to make it easier to compare the video with what we're working through.

## Maximum Sub-Array Sum of Non-adjacent Values

🌶️ Here is where things get a bit spicy, but hang in there we're nearly there!

To find all of the subsets of non-adjacent values in an array, we're going to introduce a couple of new variables: `maxInclude` and `maxExclude`, which shall be `arrays` instead of singular values (explained later). And we won't be using `maxCurrent` anymore.

### `maxInclude` and `maxExclude`

I found that a lot of folks explaining this problem began using `include` and `exclude` variables to demonstrate this problem's solution without really explaining (very well or at all) what purpose each serves.

Case in point is this video. While the dude is obviously a very bright guy, I found the explanation to be full-on Techsarcana.

{{< youtube UtGtF6nc35g >}}

That said, the terms `include` and `exclude` are used here to define the _scope of consideration_ for the value we are currently evaluating. In other words, as we loop through the array, we will either include a specific value for consideration because it is non-adjacent to the position in the array that we are currently evaluating, or we will exclude it from consideration because it is adjacent. This is what allows us to identify subsets of non-adjacent values.

#### The 4-year-old explanation

Okay, you are standing in line with a bunch of other numbers. Ignore the number directly behind you (excluding it from consideration because it is adjacent to you) and look at the number two places behind you (including it for consideration because it is not adjacent to you).

## Solution

Here's one possible solution:

```js
function maxSumNonAdjacent(arr) {
  let maxInclude = [];
  let maxExclude = [];
  maxInclude[0] = arr[0];
  maxExclude[0] = 0;
  let maxGlobal = arr[0];

  for (let i = 1; i < arr.length; i++) {
    maxInclude[i] = Math.max(maxExclude[i - 1] + arr[i], arr[i]);
    maxExclude[i] = Math.max(maxInclude[i - 1], maxExclude[i - 1]);
    maxGlobal = Math.max(maxExclude[i], maxInclude[i]);
  }
  return maxGlobal;
}
```

The above returns `8` when given the array, `[-2, 1, 3, -4, 5]`.

### Breakdown

1. Declare `maxInclude` and `maxExclude` as empty arrays.
2. Assign the first value in `maxInclude` to the first value of the given array.
3. Assign the first value in `maxExclude` to 0.
4. Declare `maxGlobal` and set its value to the first value of the given array.
5. Loop through the array starting at the second position in the array (`[i=1]`).
6. Assign `maxinclude[i]` to the largest of either:
   - the sum of `maxExclude[i - 1]` and `arr[i]`, or
   - `arr[i]`
7. Assign `maxExclude[i]` to the largest of either:
   - `maxInclude[i - 1]`, or
   - `maxExclude[i - 1]`
8. Assign the larger of `maxExclude[i]` and `maxInclude[i]` to `maxGlobal`
9. Return `maxGlobal`

### Walk-through

#### Before the loop

```console
maxInclude = [-2]
maxExclude = [0]
maxGlobal = -2
```

#### After the first the loop with `i=1`

```console
arr[i] = 1
maxInclude = [-2, 1]
maxExclude = [0, 0]
maxGlobal = 1
```

#### After the second the loop with `i=2`

```console
arr[i] = 3
maxInclude = [-2, 1, 3]
maxExclude = [0, 0, 1]
maxGlobal = 3
```

#### What's happening here?

:stop_sign: This is a good point to stop (in the middle of looping through the array) and explain what exactly is going on.

With the original array of `[-2, 1, 3, -4, 5]` when `i=2`

```console
arr[i] = 3
```

To derive `maxInclude[i]`, we:

```js
maxInclude[i] = Math.max(maxExclude[i - 1] + arr[i], arr[i]);
```

1. look at `maxExclude[i - 1]`, which is 0.
2. add that to `arr[i]`, which is 3, to get 3.
3. take the larger value of that and `arr[i]`, which is 3

To derive `maxExclude[i]`, we:

```js
maxExclude[i] = Math.max(maxInclude[i - 1], maxExclude[i - 1]);
```

1. compare at `maxInclude[i - 1]`, which is -2, to `maxExclude[i - 1]`, which is 0
2. take the larger of those two values, which is 0.

To derive `maxGlobal`, we take the larger of maxInclude[i] and maxExclude[i], which is 0.

```js
maxGlobal = Math.max(maxExclude[i], maxInclude[i]);
```

#### Continuing on follows the same process...

#### After the third the loop with `i=3`

```console
arr[i] = -4
maxInclude = [-2, 1, 3, -3]
maxExclude = [0, 0, 1, 3]
maxGlobal = 3
```

#### After the forth the loop with `i=4`

```console
arr[i] = 5
maxInclude = [-2, 1, 3, -3, 8]
maxExclude = [0, 0, 1, 3, 3]
maxGlobal = 8
```

If you made it this far, well done! :tada:

I hope you got something out of it!

## Final Thoughts

### Dynamic Programming

### Divide and Conquer
