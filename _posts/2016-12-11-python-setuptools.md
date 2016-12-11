---
layout: post
title: Using Python setuptools
category : Python
tagline: A Basic Guide
tags : [Python, Python Standard Library, web development]
---

# Python `setuptools`

The `setuptools` module deals with building and distributing Python packages.
Technically, setuptools is not part of the Python standard library. However, the
standard library package that is responsible for the same task, `distutils`, recommends 
using setuptools for distribution as it has greater functionality.

`setuptools` is complex. This guide focuses purely on the basics to get started.

### Installation

Installing setuptools is slightly more effort than usual as you can't use pip, [check the install page](https://pypi.python.org/pypi/setuptools) for
your OS requirements. In my case (OS X), I used curl to download `ez_setup.py`

`curl https://bootstrap.pypa.io/ez_setup.py -o - | python`

### Config

In your project **root directory**, create a file called `setup.py`. Add the following to the script,
adjusting to your project name/version etc.

```buildoutcfg
from setuptools import setup, find_packages

setup(
    name="your_project_name",
    version="0.0.1",
    packages=find_packages(),
)
```

You now have the bare minimum to use `setuptools`. 

### Running

To run your `setup.py` file the command format is:

`python setup.py <some_command> <options>`

to view the available commands you can type:

`python setup.py --help-commands`

You can also review the [command reference](https://setuptools.readthedocs.io/en/latest/setuptools.html#command-reference) 
which gives a pretty average explanation of the available commands.
 
Key commands include:

- `sdist` produces a raw source distribution (a gzipped tar file). Someone can 
just download this and run python setup.py directly.
- `bdist_egg` generates a Python Egg (.egg) file for the project. This is needed if someone
 wants to use `easy_install` on your project.
 - `sdist_wininst` creates an `.exe` file to install your project on Windows.
- `upload` uploads source and/or egg distributions to PyPI

For example, from my project root directory I can run:

```buildoutcfg
python setup.py sdist
```

and I will then discover a new dist folder with a gzipped tar 
file of my project inside. 

Note that in order to interact with PyPI (e.g. `python setup.py sdist upload`) you
will first need to [register an account](https://pypi.python.org/pypi)

You need to supply your username and password for pypi-related commands. You can pass these
as arguments to the setup command, but it's easier to create a `.pypirc` for this purpose. On Linux, put this file in your $HOME directory
(On Windows, you’ll need to set a HOME environ var to point to the .pypirc directory).
The `.pypirc` looks like this:

```buildoutcfg
[pypirc]
servers = pypi
[server-login]
username:your_username
password:your_password
```

### Adding functionality

We can add lots more to our `setup.py` file, for example:


```buildoutcfg
setup(
    name = "your_project_name",
    version = "0.0.1",
    author = "Joe Bloggs",
    author_email = "joebloggs@gmail.com",
    description = ("Foo bar baz"),
    license = "BSD",
    keywords = "example documentation tutorial",
    url = "http://packages.python.org/your_project_name",
    packages=['your_project_name', 'tests'],
    classifiers=[
        "Development Status :: 3 - Alpha",
        "Topic :: Utilities",
        "License :: OSI Approved :: BSD License",
    ],
)
```

Needless to say, this is just scratching the surface. Be sure to checkout [the
documentation](https://setuptools.readthedocs.io/en/latest/setuptools.html#developer-s-guide).

### Admin

The Python build system creates lots of intermediary files which you probably don't
want to commit to version control. Make sure you update your `.gitignore` file to include
the following file types:

```buildoutcfg
# Compiled python modules.
*.pyc

# Setuptools distribution directory.
/dist/

# Python egg metadata, regenerated from source files by setuptools.
/*.egg-info
```

### Optional Extras

You can configure `setup()` using a `setup.cfg` file. Typical uses include
defining package metadata, and defining options usually supplied to the `setup`
function.

e.g.

```buildoutcfg
[metadata]
name = my_package
version = attr: src.VERSION
description = My package description

[options]
zip_safe = False
```

You are now ready to distribute your Python project!