---
layout: post
title: Using the Python json Library
category : Python
tagline: A Basic Guide
tags : [Python, web development]
---

# Using the Python `json` Library: A Basic Guide

*This guide uses Python 3*

JSON (JavaScript Object Notation) is everywhere on the internet. It's a "lightweight data interchange format"
which has become one of the most common ways to send information from web applications to web browsers. If you're
doing any work involving calling web APIs, the chances are you are going to come across JSON data.

Python has a library to help you work with JSON: `json`. It's part of the Python core, so you don't have to install
it via pip, simply `import json` and you're ready to go.

Understanding how this module works will become very important for transforming your data into a useful format. Without
taking a bit of time to understand the fundamentals of this module, you are going to spend a lot of time debugging
errors such as:

#### `<foo> is not JSON serializable`


There are two key methods to understand:

1. `json.dump`/`json.dumps` (Encoding)
2. `json.load`/`json.loads` (Decoding)

A quick note on these methods, the plural versions (`dumps`/`loads`) keeps things in memory, the singular (`dump`/`load`)
write things to a file. 

This may not clairfy anything, so let's dig deeper. 

The description given in the official documentation for `json.dumps` is: 

>Serialize *obj* to a JSON formatted `str`

#### Why do we need to serialize?

Serialization is one particular type of encoding process, there are other ways to encode, but this is the one the `json` library
is concerned with. Serialization converts data, potentially objects, arrays or similar, into a single string. This
allows the data to be more easily stored and transmitted. 

When you call a web API, data has to be transmitted to you (usually via an HTTP request). So the chances are that when
that data arrives, it will be serialized (encoded). So for you to make sense of it and start manipulating it to do what 
you want, you will probably have to decode it. If you want later want to send the data onwards, you may 
need to serialize the data once again. 

#### Examples

Now that we understand what we are trying to do and why, let's look at some examples.

Serialize a Python list for transmission/storage:

```buildoutcfg
my_list = ['foo', 1, (2, 3), 'bar']
serialized_list = json.dumps(my_list)
# --> '["foo", 1, [2, 3], "bar"]'
```

If I wanted to decode my serialized Python list, I would use the reverse process (note that there is a nasty
gotcha with reversing the process when the object in question is a dictionary, [read the docs](https://docs.python.org/3/library/json.html)):

```buildoutcfg
decoded_list = json.loads(serialized_list)
# --> ['foo', 1, [2, 3], 'bar']

```

We could apply this same logic to other data structures also:

```buildoutcfg
my_list_of_dicts = [{"foo": "bar"}, {"abc": 123}]
serialized_lod = json.dumps(my_list_of_dicts)
# --> '[{"foo": "bar"}, {"abc": 123}]'
fixed_lod = json.loads(serialized_lod)
# --> [{'foo': 'bar'}, {'abc': 123}]
```

##### Common Errors

###### 1.

`TypeError: the JSON object must be str, not <data structure that is not a str>`

This what happens when you run `json.loads` on something that isn't a string, and hence is defintely not
a serialized bit of data. 

e.g.

```buildoutcfg
json.loads([1,2,3])
#TypeError: the JSON object must be str, not 'list'
```

###### 2.
`json.decoder.JSONDecodeError: Expecting value: <some line> <some column> <(some character)>`

This error gives a look at some of the internals of the `json` library, where `json.loads` and `json.dumps` use the
`JSONDecoder` and `JSONEncoder` classes respectively. This type of error occurs when you have passed in a string,
 but the string is not valid JSON (common issues include missing curly braces, missing quotation marks)

e.g. 

```buildoutcfg
json_string = '{"abc":"defg"}'
json.loads(json_string)
#No problem

non_json_string = "abcdefg"
json.loads(non_json_string)
#throws the error because the string is not JSON-like
```

###### 3.
`<foo> is not JSON serializable`

This error occurs when you try to serialize something that is not JSON serializable. A lot of things are 
"not JSON serializable", but generally this will apply to more complex objects.

e.g.

```buildoutcfg
class MyClass():
    def __init__(self, name):
        self.name = name
        
json.dumps(MyClass)
#TypeError: <class '__main__.MyClass'> is not JSON serializable
```

##### The Decoder - `json.loads`

It's important to understand that the JSONDecoder (behind `json.loads`) makes the following conversions (from JSON to Python):

![XSS Diagram]({{site.baseurl}}/assets/images/python_json_conversion_table.png)


Now you are ready to deal with the majority of JSON-related code you will have to write in
basic Python web applications.


