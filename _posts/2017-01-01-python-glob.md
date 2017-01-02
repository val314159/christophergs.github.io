---
layout: post
title: Using Python glob
category : Python
tagline: A Basic Guide
tags : [Python, Python Standard Library, web development]
---

# Python `glob`

`glob` is described in [the docs](https://docs.python.org/3/library/glob.html) as:
 "Unix style pathname pattern expansion"...So what is that?

Basically, anytime your program needs to look for a list of files on the filesystem, and the
names of these files match a pattern, then `glob` will help you get the task done.

### The `glob` API

The API for using `glob` is brief, with three main methods:

`glob.glob(pathname, *, recursive=False)`
The main guy: Returns a list of pathnames depending on the search criteria passed in with "pathname"

`glob.iglob(pathname, recursive=False)`
Same as `glob.glob`, except that it returns an iterator, meaning that not all values get stored in memory - so
can be much more efficient.

`glob.escape(pathname)`
Escapes special characters in the passed in "pathname".

That's it! This is a succinct yet powerful module.


### Basic Usage

The pattern rules for glob are *not* regular expressions. You've got the asterisk (*) wildcard for searching all files within
a given directory without dropping into subdirectories.

If you have a directory structure like this

```
.
+-- glob_post_dir
|       filea.txt
|       fileb.txt
|       filec.txt
|       readme.txt
|       glob_post.py
|-------subdirectory
|           filed.txt
|           filee.txt
```

and `glob_post.py` looks like this:

```
import glob
import os

CWD = os.getcwd()

for name in glob.glob(CWD+'/*'):
    print(name)
```
The output will be the full file paths of the five files directly under the `glob_post_dir` directory, but **not** the files 
in the subdirectory. To get the paths for the subdirectory files, we will need to adjust the code as follows:

```
# ...

for name in glob.glob(CWD+'/subdirectory/*'):
    print(name)

# or

for name in glob.glob(CWD+'/*/*'):
    print(name)
```

Note that neither of these return the file paths of those files in the parent directory.

If we wanted to get just `filea.txt`, `fileb.txt` and `filec.txt` then the single character wildcard, the question mark (`?`),
is the way to go:


```
# ...

for name in glob.glob(CWD+'/file?.txt'):
    print(name)
```

### More Advanced Usage


**Character ranges**

Use character ranges to match a specific character. A dash represents an unbroken range.

If we have this directory structure:

```
.
+-- glob_post_dir
|       file1.txt
|       file2.txt
|       file5.txt
|       glob_post.py
|-------subdirectory
|           filed.txt
|           filee.txt
```

*glob_post.py*

```
import glob
for name in glob.glob(CWD+'/*[1-2].*'):
    print name

# or

for name in glob.glob(CWD+'/*[12].*'):
    print name

```
The above code will return only the paths for `file1.txt` and `file2.txt` due to the character range specified.


**Recursive Search**

Starting with Python version 3.5, the glob module supports the "**" directive (which is parsed only if you pass recursive flag).

In our directory structure above, we could code the following in `glob_post.py`:

```buildoutcfg
import glob
import os

CWD = os.getcwd()

for name in glob.glob(CWD+'/**/*', recursive=True):
    print(name)
```

This code will return the paths for all files in both the parent and its subdirectory.

### See also

[`fnmatch`](https://docs.python.org/3/library/fnmatch.html#fnmatch.fnmatch): `glob` uses `fnmatch.fnmatch` under the hood.

For recursive file selection in older versions of Python, [see this discussion](http://stackoverflow.com/questions/2186525/use-a-glob-to-find-files-recursively-in-python).
