---
layout: post
title: Overview of Python Generators: Part 2
category : Python 
tagline: A Basic Guide
tags : [Python, Python Key Concepts]
---

# Overview of Python Generators Part 2: Generator Comprehensions

If you missed part 1, here it is

Imagine we have a list comprehension:

```buildoutcfg
list_comp = [n for n in range(100)]
```

Clearly this will create a list from 0 to 99. It's easy to understand because you just type
into the console `list_comp` and there is the whole list, right in front of you. 
The thing is, Python will hold the entirety of this list in memory. For a tiny range like 0-100,
this is not a problem. But if we were dealing with large numbers,or complex operations, 
this would be expensive. Enter the generator expression:

```buildoutcfg
my_generator = (n for n in range(100))
```

now if we type `my_generator` we see `<generator object <genexpr> at 0x100666a40>` instead of the
list. This object is a generator iterator. At its most basic level, this generator iterator is an
object on which you can call `next`. What goes on under the covers is that you keep calling next
until the object raises a `StopIterationError`, which means that all its values have been generated.

So in our example, if we want to fetch the values, we need to iterate over the generator object.
 This iteration is done lazily - i.e. on demand. Because of this, the memory usage is far less taxing.
 Here's how it works:
 
 ```buildoutcfg
next(my_generator)
# >>> 0
next(my_genreator)
# >>> 1
```

To get everything at once:
```buildoutcfg
for n in my_generator:
    print(n)
```
And you see the whole 0-99 range printed.


A Python generator is a function which returns a generator iterator

All this magic relies on the `yield` statement, which allows execution state to be stored
and resumed at a certain point.

First of all the term generator originally was somewhat ill-defined in Python, 
leading to lots of confusion. What you probably mean are iterators and iterables (see here). 
Then in Python there are also generator functions (which return a generator object), 
generator objects (which are iterators) and generator expressions 
(which are evaluated to a generator object).

According to http://docs.python.org/glossary.html#term-generator it seems that the official terminology is now that generator is short for "generator function". In the past the documentation defined the terms inconsistently, but fortunately this has been fixed.

It might still be a good idea to be precise and avoid the term "generator" without further specification.


### Generator Gotchas

Effective Python Examples