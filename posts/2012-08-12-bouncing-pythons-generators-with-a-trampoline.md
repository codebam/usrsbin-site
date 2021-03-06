---
title: Bouncing Python's Generators With A Trampoline
author: Alex Beal
date: 08-12-2012
type: post
permalink: Bouncing-Python-s-Generators-With-A-Trampoline
desc: Tail call optimize recursion in Python using generators.
---

In this post, I'm going to combine two of my favorite topics: recursion and Python's generators. My aim is to address a real pain point in Python: its lack of tail call optimization. Let's start with a simple `countdown` function that counts from `start` to `0` using recursion:

``` python
def countdown(start):
    print start
    if start == 0:
        return 0
    else:
        return countdown(start - 1)
```

This function needs no explanation. It simply prints `start` and then calls itself with `start - 1`. The issue here is that for large values of `start`, we can easily blow Python's stack:

``` python
% countdown(1000)
1000
999
[...]
3
2
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 6, in countdown
[...]
RuntimeError: maximum recursion depth exceeded
```

Of course, one option is to just loop:

``` python
print "\n".join([str(i) for i in xrange(1000,-1,-1)])
```

But I didn't write this post just to tell you that recursive functions can be translated into loops (duh). Instead, I'm going to use a technique called trampolining to convert tail-recursive functions into tail-recursive generators, which won't blow the stack.

Returning to the countdown example, let's think about why the recursive call is a problem in the first place. The issue is that in `return countdown(start - 1)`, the call to `countdown` must complete before its value can be returned, thus increasing the call stack. "Well, of course," you say. "That's how recursive functions work." But wait a minute. Is that really necessary? What if, rather than `countdown` calling itself, it simply *returned the next recursive call in the sequence* for its caller to execute. Hey, I think we might be on to something:

``` python
def countdown(start):
    print start
    if start == 0:
        yield 0
    else:
        yield countdown(start - 1)
```

So now `countdown` is a generator which calls itself. That's kind of wacky. Let's call it and see what happens.

``` python
% countdown(5)
<generator object countdown at 0x100492550>
```

That's interesting. We now have a generator. Think about that for a moment. What does this generator represent? Ah, that's right. *It's the next recursive call in the sequence.* It just hasn't been executed yet. Let's execute it:

``` python
% g = countdown(5)
% g = g.next()
5
```

We're getting somewhere.

``` python
% g = countdown(5)
% g = g.next()
5
% g = g.next()
4
% g = g.next()
3
% g = g.next()
2
% g = g.next()
1
% g = g.next()
0
% g = g.next()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: 'int' object has no attribute 'next'
```

Well, that's really neat. Each generator represents one recursive call, and a generator's return value is the next recursive call in the sequence, except for the last call. What happened there? We reached the base case, and the last generator returned `0`.

We now have a general pattern. Let's write a function which executes these tail-recursive generators automatically:

``` python
import types

def tramp(gen, *args, **kwargs):
    g = gen(*args, **kwargs)
    while isinstance(g, types.GeneratorType):
        g=g.next()
    return g
```

So, a generator function is passed in, which is then used to create the generator `g`. `g.next()` is repeatedly called, and the next recursive call is stored back to `g`. We keep doing this until `g` no longer returns a generator. If `g` is not a generator, then we've reached the base case, and we return it (if the base case is a generator, you'll have to use something like exceptions to signal that you've reached the base case).

**Note:** It's about time that I explained where the word "trampoline" comes from. The idea is that each recursive call is "trampolined" off the trampoline runner (the `tramp` function in this case). That is, each recursive call is returned to `tramp` and is bounced off of it. I also like to think that each recursive call bounces higher (just like on a trampoline) because the "virtual" recursion depth increases, but it's very possible that I made that up in my head.

Let's try this with countdown, and a large `start` value:

``` python
% tramp(countdown, 1000)
1000
999
[...]
2
1
0
0   <--- Since we executed in the interactive interp,
         this is the return val of `tramp`.
```

Excellent! The original function blew the stack, but the trampolined generator does just fine. Furthermore, we've written a general trampoline runner to execute these trampolined generators. But, before we call it a day, we have to write a trampolined version of the Fibonacci function, otherwise, *what's even the point of recursion?*

``` python
def fib(count, cur=0, next_=1):
    if count <= 1:
        yield cur
    else:
        # Notice that this is the tail-recursive version
        # of fib.
        yield fib(count-1, next_, cur + next_)
```

Let's take this for a test drive:

``` python
# Print the first 10 Fibonacci numbers.
for i in xrange(1,11):
    print tramp(fib, i)

Output:
0
1
1
2
3
5
8
13
21
34
```

Well, that's terrific. Not only is the tail-recursive version just as efficient as the `for-loop` version (except, of course, for function calling, and generator overhead), it also won't blow the stack.

So, this is yet another example of the power of Python's generators (Python's most under appreciated feature, as David Beazley argues). What are the takeaways?

1. This technique only works for *tail-recursive* functions. The recursive call must be the last thing the function does.
2. The translation to a trampolined generator is easy! Just turn the `return` statements into `yield` expression.
3. Although this will protect your stack, creating a generator for each call is probably slow.

If this was fun for you, then definitely check out David Beazley's [presentation on coroutines and concurrency](http://www.dabeaz.com/coroutines/index.html). He uses trampolining, generators, and coroutines to write an OS in Python, complete with a task scheduler, sys calls, and blocking and non-block IO. This man is a wizard.

Eli Bendersky also has a couple great articles on using generators for [lexical scanning](http://eli.thegreenplace.net/2012/08/09/using-sub-generators-for-lexical-scanning-in-python/) and [state machines](http://eli.thegreenplace.net/2009/08/29/co-routines-as-an-alternative-to-state-machines/). Those posts inspired me to write a [command line argument parser](https://gist.github.com/3325354) using coroutines, which can process a stream of characters and handles all the usual suspects, like quotes and escape characters. As you can tell, I've been getting quite a bit of mileage out of these techniques.
