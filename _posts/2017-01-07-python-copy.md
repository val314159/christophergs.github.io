---
layout: post
title: Using Python copy
category : Python
tagline: A Basic Guide
tags : [Python, Python Standard Library, web development]
---

# Python `copy`

### Introduction

The background to `copy` in [the docs](https://docs.python.org/3/library/copy.html) makes the point: 

>Assignment statements in Python do not copy objects, they create bindings between a target and
an object

This is a super important Python concept. Instead of variables (in the classic sense), Python has names and bindings.
When you do:

```buildoutcfg
foo = 1
```

Then you are binding the name `foo` to the object `1`. To check that `1` really is an object, just enter
`dir(1)` and look all the attributes and methods available. So if you then say:

```buildoutcfg
bar = foo
```

Then `bar` is bound to the same object as `foo`. 
In practice, this only really matters when it comes to mutable objects, basically lists and classes. 
With integers, floats, tuples and strings, things behave intuitively:

```buildoutcfg
foo = 1 
bar = foo
print(bar)
# >>> 1
foo = 2
print(foo)
# >>> 2
print(bar)
# >>> 1
# expected behavior - bar behaves as a copy
```

But this will cause you pain when it comes to to mutable objects (note that strings and integers are not mutable):

```buildoutcfg
foo = [1, 2, 'baz']
bar = foo
print(bar)
# >>> [1, 2, 'baz']

foo[2] = 'pepper steak'
print(bar)
# >>> [1, 2, 'pepper steak']
# GOTCHA - the bar value has *also* changed because it is bound to the same mutable object
```

Enter the `copy` library, which allows us to achieve the behavior we want

There are two key types of copying to understanding: "deep" and "shallow", let's explore them:

### Shallow Copy

By default the `copy.copy()` method returns a shallow copy. 

In our example above, if we made the following adjustment:

```buildoutcfg
from copy import copy
foo = [1, 2, 'baz']
bar = copy(foo)
print(bar)
# >>> [1, 2, 'baz']
foo[2] = 'peper steak'
print(bar)
# >>> [1, 2, 'baz']
# This time bar is a copy as we expect
```

### Deep Copy

At first glance, `deepcopy` and `copy` have similar behavior. However, when working with nested structures, 
you may find yourself in need of a `deepcopy`. A "shallow" copy of a nested object (or compound object) creates
a new copy and inserts references to the original. "References" means that things can go wrong. On the other hand,

>A deep copy constructs a new compound object and then, recursively, 
inserts copies into it of the objects found in the original.

No referencing. Full whack copies.
 
Time for code!

If you have list of lists:

```buildoutcfg
import copy
nest_list = [1, 2, ['nested1', 'nested2']]
shallow = copy.copy(nest_list)
deep = copy.deepcopy(nest_list)

print(shallow)
# >>> [1, 2, ['nested1', 'nested2']]
print(deep)
# >>> [1, 2, ['nested1', 'nested2']]
nested_list[0] = 9
print(shallow)
# >>> [1, 2, ['nested1', 'nested2']]
print(deep)
# >>> [1, 2, ['nested1', 'nested2']]
nested_list[2][0] = 'nested9'
print(shallow)
# >>> [1, 2, ['nested9', 'nested2']]
# CHANGED
print(deep)
# >>> [1, 2, ['nested1', 'nested2']]
# STAYED THE SAME
```

So if you need a reliable copy of a compound object (most likely to be a class) 
with lots of nested attributes, then your best bet is `deepcopy`


