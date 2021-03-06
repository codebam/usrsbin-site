---
title: Building Data Structures from Functions
author: Alex Beal
date: 04-01-2012
type: post
permalink: Building-Data-Structures-from-Functions
---

Here's a puzzle I've adapted from [exercise 2.4](http://mitpress.mit.edu/sicp/full-text/book/book-Z-H-14.html#%_sec_2.1.3) of SICP.

> Suppose you are programming in a language that only supports function application. That is, defining functions and applying arguments to these functions are the only things this language supports. Using only these building blocks, could you construct a linked list?

Surprisingly, the answer is yes, and the exercise linked to above provides a partial solution. Below, I've translated that solution into Python, and completed the exercise:

```python
# Create a pair from l and r
def cons(l, r):
    return lambda get: get(l, r)
# Given a pair, return the head (left) value.
def head(pair):
    return pair(lambda l, r: l)
# Give a pair, return the tail (right) value.
def tail(pair):
    return pair(lambda l, r: r)
```

First, let's examine how these functions can be used, and then I'll explain how they work. Consider the snippet below:

```python
l1 = cons(1, 2)
print head(l1)       # Prints 1
print tail(l1)       # Prints 2
l2 = cons(0, l1)
print head(l2)       # Prints 0
print head(tail(l2)) # Prints 1
print tail(tail(l2)) # Prints 2
```

As can be seen, `cons()` is the constructor for this pair type. Give it two values, and it will create a pair of those two values. `head()` and `tail()` are the basic operators that let us access values inside these pairs; they return the left and right element of the pair, respectively. Also notice that we can create pairs of pairs. The last half of the example creates a pair composed of `0` and `(1,2)`. Why is this significant? Well, we've just made a linked list! Linked lists are simply pairs of pairs. The list `[1,2,3,4]` can, for example, be represented as `cons(1,cons(2,cons(3,cons(4,None))))`. What's `None` doing at the end of the list? You can think of it like the `NULL` pointer at the end of a linked list in C. If a function were traversing the list, `None` would signify to the function that it has reached the end. Mathematically, you can think of a linked list as an inductively defined data structure, where `None` is the base case. `None` is referred to as the *empty list*.

Now for the interesting part. How do these functions work? First let's look at the `cons()` function:

```python
# Create a pair from l and r
def cons(l, r):
    return lambda get: get(l, r)
```

This takes in two parameters (`l` and `r`) and returns a function.<sup>1</sup> This returned function takes yet another function, which it applies to `l` and `r` and returns the result. So, if we call `cons(1,2)`, we are returned a function, which takes a function and applies that function to the arguments `1` and `2`. If, for example, I called `cons(1,2)(lambda x, y: x+y)` I'd get `3`, because the addition function would be applied to `1` and `2`.

Now suppose that we didn't want to add `l` and `r`. Instead, we wanted to pull either `l` or `r` out of a pair that was already constructed. In other words, given `list = cons(1,2)`, how could we pull the `1` or `2` out of the function stored in `list`? Well, all we need to do is pass it a function that returns only the left or right parameter. So, `cons(1,2)(lambda l, r: l)` would give us back `1`, and `cons(1,2)(lambda l,r: r)` would give us back `2`. This is almost exactly what the `head()` and `tail()` functions are doing! They take in a function (presumably produced by the `cons()` function), and apply either `lambda l,r: l` or `lambda l,r: r`. Just to cement this all together, below I step through the evaluation for the example `head(cons(1,2))`.

```python
head(cons(1,2))
-> head(lambda get: get(1, 2))
-> (lambda get: get(1, 2))(lambda l,r: l)
-> (lambda l,r: l)(1,2)
-> 1
```

And here is a bit of code that uses these new functions to add the elements of a list:

```python
def sum_list(l):
    return head(l) if tail(l) == None else head(l) + sum_list(tail(l))
sum_list(cons(1,cons(2,cons(3,None)))) # Returns 6
```

In the end, this might strike you as nothing more than a useless programming trick. In a sense that's right. I'd never use this in my own code. What makes this technique so valuable is that it actually fits into the broader context of lambda calculus, which is a mathematical abstraction of computation. In the language of lambda calculus, you're given only a very basic set of tools. Essentially all you have are simple one parameter functions (it doesn't even have integers!). Incredibly, it turns out that that's all you need for a Turing complete language. It's possible to build integers, conditionals, loops, arithmetic operations, and (as you've seen here) data structures out of these simple building blocks. As a next step, and to see this for yourself, it might be fun to take a minute and see how the code presented here could be modified to give you binary trees, or even graphs. After that, you could check out this incredible article on writing the infamous [FizzBuzz](http://www.codinghorror.com/blog/2007/02/why-cant-programmers-program.html) exercise [in Ruby using only functions](http://experthuman.com/programming-with-nothing).

###Notes###
1. Technically, this is really a closure, which is a function with some of its variables already set. In the terminology of the PL world, it's a function bundled with its environment (the set of variables in its outer scope).
