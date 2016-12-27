---
layout: post
title: Using Python unittest.mock
category : Python
tagline: A Basic Guide
tags : [Python, Python Standard Library, web development]
---

# Python `unittest.mock`

As you test your Python code, there will be operations which are slow and painful to test. These are usually bits of code that do things with the filesystem or involve network access. `unittest.mock` provides a way to mimic these bits of 'slow' code, so that your test suite can run fast, or to prevent you from having to write very complex tests. 


### Installation

After Python version 3.3, mock was included as part of the `unittest` library. If you are using an older version of Python, you will need to install it with `pip` and import `mock`.

### Basics

`unittest` requires quite a bit of boilerplate. If you're not familiar with this built in library, [read up on it first](https://docs.python.org/3/library/unittest.html). Here's a simple example of how to mock a function, and test that the function was called with certain paramaters:

```
import unittest

class SomeClass(object):
    pass

class TestMock(unittest.TestCase):
    def setUp(self):
        self.real = SomeClass()
        self.real.expensive_method = unittest.mock.MagicMock(name='method')
        self.real.expensive_method(3, 4, 5, key='value')

    def test_basics(self):
        # Causes tests to fail because key is not set to 'value'
        self.real.expensive_method.assert_called_with(3, 4, 5, key='value2')

if __name__ == '__main__':
    unittest.main()
```

Run the tests here and they will fail until you change the key parameter of the `expensive_method`. This example shows how we can mock out a method, which would be useful if our `expensive_method` was doing something time consuming such as posting a message via the facebook API. For this kind of use case, the docs note that `Mock` and `MagicMock` classes are interchangeable, and recommend using `MagicMock` as default.

In addition to mocking the parameters of a function or method, we can also mock the return values. In the above example, this would require a the following additions:

```
# ... (same as above)

    def setUp(self):
        # ...
        self.real.method.return_value = 42

    def test_basics(self):
        self.real.method.assert_called_with(3, 4, 5, key='value')
        assert self.real.method() == 42

# ... (same as above)
```

Similar to returning certain values, you can also raise specified exceptions using `side_effect`:

```
mock = Mock(side_effect=Exception('Boom!'))

mock()
Traceback (most recent call last):
  ...
Exception: Boom!
```


### More Advanced Usage

`mock` can be used to mock entire classes by calling `patch`. By doing this, the entire class is swapped out for a special mock object. This is a convenient way to write a monkey patch.

```
import unittest
from unittest import mock

class SomeClass(object):
    def __init__(self):
        print('Created SomeClass {}'.format(id(self)))

    def my_method(self):
        return 42


class TestMock(unittest.TestCase):
    def create_instance(self):
        return SomeClass()

    def setUp(self):
        self.legit = self.create_instance()

    def test_my_method(self):
        assert self.legit.my_method() == 42
        with mock.patch('__main__.SomeClass') as m:
            instance = m.return_value
            instance.my_method.return_value = 777
            result = self.create_instance()
            assert result.my_method() == 777

if __name__ == '__main__':
    unittest.main()

```

In this example, we are mocking `SomeClass` and fixing the value returned by one of the class methods, `my_method` to the value 777.


It is also possible to acheive the same effect using a decorator:

```
class SomeClass(object):
    def __init__(self):
        print('Created SomeClass {}'.format(id(self)))

    def my_method(self):
        return 42


class TestMock(unittest.TestCase):
    def create_instance(self):
        return SomeClass()

    def setUp(self):
        self.legit = self.create_instance()

    @mock.patch('__main__.SomeClass')
    def test_my_method(self, mock_class):
        mc = mock_class.return_value
        mc.my_method.return_value = 777
        self.mocked = self.create_instance()
        
        assert self.mocked.my_method() == 777

if __name__ == '__main__':
    unittest.main()
```

**Note**

When you use either a context manager or decorator (as in the above two examples), they handle the clean up of the patched object for you. If you need to patch in another way, be sure to call `start` and `stop`, or else you risk having a monkey patched object throughout the rest of your tests.

---

This a very brief look at the `mock` library, be sure to [check the docs](https://docs.python.org/3/library/unittest.mock-examples.html) for information about nested patching, mocking generators and dictionaries, mocking imports, and more.


