---
layout: post
title: Overview of Python Generators: Part 1
category : Python 
tagline: A Basic Guide
tags : [Python, Python Key Concepts]
---

# Python Generators

### Introduction

Most Python developers know about generators, but don't really use them very often. They are
aware of the `yield` statement, but if you read their code, it tends not to feature.

Let's do away with this hesitation and take a close look at how to use these powerful Python
tools. 

To begin, let's clarify some terminology. A *generator* is a particular type of function which
returns an object called a *generator iterator*. For those with experience in other languages, it may be
helpful to equate a generator with a coroutine, although Python does not use that term. It might also help to
highlight that generators are aa form of lazy evaluation. You will also hear generator iterators referred to
as simply *iterables* or *iterators*. Confusingly, generator iterators are usually just shortened
to *generators*. A 
*generator function* is a particular type of generator which makes use of the `yield` statement 
(even if it also has a `return` statement it is still classed as a generator function).


### What is `yield`?

A function which uses the `yield` statement does not return a value in the standard sense.
In a normal function, execution goes until either a `return`, `Exception` of some kind, or
it reaches the end of the function and returns an inplicit `None`. 
However with `yield` you temporarily pass
the execution back outside the function, whilst maintaining a memory of the work you have done
within the function - usually iterating over something. This creates a powerful ability to
"pause" your work mid-flow, rather than the usual all or nothing options. It is helpful to think
of `yield` as a fancy `return` statement.

OK, time for some code. Here's a basic **generator function**:

```buildoutcfg
def basic_generator_function():
    yield 'foo'
    yield 'bar'
```

As clarified above, this function returns a generator iterator. In order to use the function, we can either 
loop over it, or use the built-in function for iterators: `next()`

```buildoutcfg
for word in basic_generator_function():
    print(word)
    
# foo
# bar

# Alternatively:

example_generator = basic_generator_function()

next(example_generarator)

# foo

next(example_generator)

# bar
```

Once you have looped over all the values using `next()`, you will receive a `StopIteration` Error. This means
you can only consume the values of a generator once.

The above example does not really convey the power of a generator function. So let's increase the complexity
slightly. Let's say you are trying to calculate the fibannaci numbers (one of the many interesting answers
[here on stackoverflow](http://stackoverflow.com/questions/102535/what-can-you-use-python-generator-functions-for)):

```buildoutcfg
def fib():
    first=0
    second=1
    yield first
    yield second

    while 1:
        next_fib=first+second
        yield next_fib
        first=second
        second=next_fib

fibgen1=fib()
fibgen2=fib()

# note that .next() is alternative sytnax to next(fibgen1)
>>> fibgen1.next(); fibgen1.next(); fibgen1.next(); fibgen1.next() 
0
1
1
2
>>> fibgen2.next(); fibgen2.next()
0
1
>>> fibgen1.next(); fibgen1.next()
3
5
```

We can see that the generator function retains the value of "next_fib", and by maintaining the value of the state
in this way we can start the calulations deep in the sequence. 

We also use the `while` loop to prevent the generator from exhausting itself, this is a common idiom when dealing
with potentially infinite sequences. If we wanted to we could adjust the function to start/stop returning values
of a certain size range, we can also pass values (such as any limits we wish to impose) into the generator function.

In the next section we will look at generator comprehensions.


