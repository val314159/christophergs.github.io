---
layout: post
title: Using Python errno
category : Python
tagline: A Basic Guide
tags : [Python, Python Standard Library, web development]
---

# Python `errno`

### Introduction

You probably won't come across `errno` until you have been using Python for a little while. 
When you do, it may not be immediately clear why you need it.

Here's a code snippet to view the full range of errors:

```buildoutcfg
import errno
import os

print {i: os.strerror(i) for i in sorted(errno.errorcode)}
```

The docs give the following intro:

> This module makes available standard errno system symbols. 
The value of each symbol is the corresponding integer value. 
The names and descriptions are borrowed from linux/include/errno.h, 
which should be pretty all-inclusive.

OK, but why do we need these guys? We can already raise exceptions, such as `IOError` or
`TypeError`...

Well, what you need to know is that most of the standard exceptions cater for a "bucket" of 
different errors, and there will be times when you need to be able to distinguish between
different errors which are from the same "bucket". You could also create a custom exception,
but often times this is overkill.

### (Bad) Example

Both 'invalid path' and 'file does not exist' fall under `IOError`. 

Here's how we would distinguish between them:

**file does not exist**

```buildoutcfg
import errno
import os

try:
	my_file = open('no.txt')

except IOError, e:
    # 'No such file or directory'
    if e.errno == 2:
        print(e.strerror)
        print("this will print")
        # handle one way
    elif e.errno == 9:
        print(e.strerror)
        print("this will not print")
        # handle another way
        
```

### Avoiding Confusion

The above code will run fine, but we should ensure that our code is
portable. If we compare against specific errno values (which may
be different on other OS/platforms) then we may run into problems

Instead, we compare against the names of the errors, which the 
`errno` library provides. Let's re-write the above code
 to take this into consideration:
 
```buildoutcfg
import errno
import os

try:
	my_file = open('no.txt')

except IOError, e:
    # 'No such file or directory'
    if e.errno == errno.ENOENT:
        print(e.strerror)
        print("this will print")
        # handle one way
    elif e.errno == errno.EBADF:
        print(e.strerror)
        print("this will not print")
        # handle another way
        
```

If we ever need to get the human readable form of an error,
then we use `os.strerror`

```
os.strerror(errno.EBADF)`
# >>> 'Bad file descriptor'
```

Now we have achieved cross-platform clarity with our error
handling.