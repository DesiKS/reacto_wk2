
# Reverse a Linked List

---

## Interviewer Prompt

```js
const mkNode = (value, next = null) => ({ value, next })
const nums = mkNode(1, mkNode(2, mkNode(3)))
```
Given a linked list with `value` and `next` properties, do the following:
- Part 1. Log the values in reverse order.
- Part 2. Write a function which returns a new reversed linked list.

**Restrictions**:

This is a functional programming exercise! Hence, the following restrictions apply:
- The input linked list cannot be mutated, part 2 needs to be a 100% pure function 
- Do not use any built-in data structures or methods, only variables and functions, i.e.
  - no arrays, no objects except the (immutable) list nodes themselves
  - no iterables or iteration protocol (`for`-`of`, `...`, etc.)

---

## Example Output

Part 1:

```js
const nums = mkNode(1, mkNode(2, mkNode(3)))
logReverse(nums)
```

Output:

```
3
2
1
```

Part 2:

```js
const nums = mkNode(1, mkNode(2, mkNode(3)))
const reversed = reverse(nums)
console.log(reversed)
```

Output:

```
{ value: 3, next: { value: 2, next: { value: 1, next: null } } }
```

---


## Interviewer Guide

---

### RE -

* Give Part 1 before hinting that there is a Part 2 (if you can).
* The list is singly-linked – it only has `value` (or `head`) and `next` (or `tail`) properties.
* The list can be implemented as a class, or a factory function, or just manually-constructed objects. 

---

### - AC -

* List nodes can be constructed, but _not_ changed after construction.

```js
// OK:
const newNodeOK = { value: something, next: oldNode }
// NOT OK:
const newNodeBad = { value: something }
newNodeBad.next = oldNode
```

* Parameters and variables can be _defined_ but not _reassigned_. Parameters _may_ have default values (but it is not strictly necessary to solve the problem).
* If they are hitting a complete wall, point out that instead of loops, functional programming languages use recursion.
* Think of `null` as "empty list" – in practice it is a cleaner and more natural base case for recursion than the last "occupied" node.
* For Part 2, if they are trying to re-use existing nodes, help guide them towards the realization that it is impossible to re-use existing nodes in a reversed list without mutating those nodes.

---

### - TO

- Ensure solution has no reassigned variables, parameters, or properties
- Ensure solution uses no built-in data structures or methods (except the node objects themselves)
- Best possible time & space for Part 1: `O(n)` time, `O(n)` stack space
- Best possible time & space for Part 2: `O(n)` time, `O(n)` space
  - Have to construct an all-new list of n nodes, that takes time and space!

---

### Answers to Common Questions

- Is recursion actually better than loops?
  - In JavaScript not necessarily. Sometimes recursion is cleaner or more natural looking, but it is rarely faster or smaller than an imperative loop. In functional languages you don't have the option of a loop, but a _tail-recursive_ function gets optimized into a loop by the compiler anyway.
- So what's the point?
  - Functional programming isn't just "normal programming minus some conveniences" – by structuring code in terms of functions, static typing, purity, etc. you actually also unlock and gain new tools for static analysis (dev tools and enforced correctness), composability, ability to reason about code in terms of laws, etc. 
- How do I account for recursion in Big O?
  - The space complexity due to recursion is a factor of the maximum height of the call stack (i.e. the deepest recursive branch). The time complexity can be harder to analyze as it depends on whether you have multiple recursive calls – you need a way to figure out how many recursive calls you will use in total.

---

## Part 1 Solution

```js
// O(n) time, O(n) stack space
const logReversed = list => {
    if (!list) return
    logReversed(list.next)
    console.log(list.value)
}
```

---

## Part 2A: Expected / Efficient Solution (Implicit Fold)

```js
// O(n) time, O(n) space, tail-recursive
const reverse = (oldList, newList = null) => {
    if (!oldList) return newList
    const newerList = mkNode(oldList.value, newList)
    return reverse(oldList.next, newerList)
}
```

In functional programming languages, you cannot have "optional" parameters, so you'd do this:

```js
const reverse = initList => {
    const go = (oldList, newList) => {
        if (!oldList) return newList
        const newerList = mkNode(oldList.value, newList)
        return go(oldList.next, newerList)
    }
    return go(initList, null)
}
```

---

## Part 2B: Functional Programming Veteran Solution (Explicit Fold, Currying, Combinators)

This is NOT an expected REACTO solution! It's an example of what an experienced functional programmer might translate from similar built-in functions in e.g. Haskell.

```js
const foldl = (combine, accum) => list => {
    if (!list) return accum
    const newAccum = combine(accum, list.value)
    return foldl(combine, newAccum)(list.next)
}

const flip = f => (a, b) => f(b, a)

const reverse = foldl(flip(mkNode), null)
```

---
