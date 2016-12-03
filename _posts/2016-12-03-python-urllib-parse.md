---
layout: post
title: Using urllib.parse in Python
category : Python
tagline: A Basic Guide
tags : [Python, Python Standard Library, web development]
---

In Python 3, `urllib.parse` contains lots of functions for doing things with URLs (note that in Python 2, these are not organized in quite the same way):

- `urlparse`
- `parse_qs`
- `parse_qsl`
- `urlunparse`
- `urlsplit`
- `urlunsplit`
- `urljoin`
- `urldefrag`
- `quote`
- `quote_plus`
- `quote_from_bytes`
- `unquote`
- `unquote_plus`
- `unquote_to_bytes`
- `urlencode`

What follows is a very brief explanation and usage example of each, to give a flavor of the 
ways this library can be helpful. 

In the examples, I've omitted the imports for brevity, make sure you do `from urllib.parse import <function>`. 

Once you've got a feel
for which method you need, you can [read the full documentation](https://docs.python.org/3/library/urllib.parse.html#module-urllib.parse)

### Parsing Functions

All the URL parsing functions operate on strings, but also bytes or bytearrays. However, you must
not mix strings and bytes in a single function call or you will trigger a `UnicodeDecodeError`

#### urlparse
Parses the URL into six components. Note the use of `geturl()` which can also be applied to the
 results of `urlsplit` and `urldefrag`

```buildoutcfg
parsed_url = urlparse('https://docs.python.org/3/library/urllib.parse.html#module-urllib.parse')
parsed_url
# ParseResult(scheme='https', netloc='docs.python.org', path='/3/library/urllib.parse.html', params='', query='', fragment='module-urllib.parse')
parsed_url.path
# '/3/library/urllib.parse.html'

# view original url
original_url = parsed_url.geturl()
# 'https://docs.python.org/3/library/urllib.parse.html#module-urllib.parse'
```

#### parse_qs
Parse a query string part of a URL, returning a dictionary of the data. Often used in
conjunction with `urlparse` as it does not find the query string part of a URL on its own.

```buildoutcfg
parsed_url = urlparse('http://www.youtube.com/watch?v=_oPAwA_Udwc&feature=feedu')
q = parse_qs(parsed_url.query)
# {'feature': ['feedu'], 'v': ['_oPAwA_Udwc']}
```

#### parse_qsl
Same functionality as `parse_qs` except that it returns a list of key-value pairs, not a dict. 

```buildoutcfg
parsed_url = urlparse('http://www.youtube.com/watch?v=_oPAwA_Udwc&feature=feedu')
q = parse_qsl(parsed_url.query)
# [('v', '_oPAwA_Udwc'), ('feature', 'feedu')]
```

#### urlunparse
If you have a regular tuple of values, `urlunparse` will combine them to form a URL - i.e. undo
`urlparse`

```
parsed_url = urlparse('https://docs.python.org/3/library/urllib.parse.html#module-urllib.parse')
parsed_url
# ParseResult(scheme='https', netloc='docs.python.org', path='/3/library/urllib.parse.html', params='', query='', fragment='module-urllib.parse')
normal_url = urlunparse(parsed_url)
normal_url
# 'https://docs.python.org/3/library/urllib.parse.html#module-urllib.parse'
```

#### urlsplit

Very similar to `urlsplit` except that it does not split the params from the URL, meaning that 
it returns 5 components instead of 6.

```buildoutcfg
split_url = urlsplit('https://docs.python.org/3/library/urllib.parse.html#module-urllib.parse')
split_url
# SplitResult(scheme='https', netloc='docs.python.org', path='/3/library/urllib.parse.html', query='', fragment='module-urllib.parse')
```

#### urlunsplit

Combine elements of the tuple created by urlsplit back into a complete URL

```
split_url = urlsplit('https://docs.python.org/3/library/urllib.parse.html#module-urllib.parse')
split_url
# SplitResult(scheme='https', netloc='docs.python.org', path='/3/library/urllib.parse.html', query='', fragment='module-urllib.parse')
normal_url = urlunsplit(split_url)
normal_url
# 'https://docs.python.org/3/library/urllib.parse.html#module-urllib.parse'
```

#### urljoin

Construct a complete/new URL from a base and an additional string. Note that when working
with URLs, you want to use this method and not `os.path.join` as the latter will not give the
desired effect on Windows

```buildoutcfg
base_url = 'https://docs.python.org'
addition = '3/library/urllib.parse.html#module-urllib.parse'
url = urljoin(base_url, addition)
url
# 'https://docs.python.org/3/library/urllib.parse.html#module-urllib.parse'
```

#### urldefrag

If the URL has a fragment, this function splits the fragment and the rest of the URL, returning a tuple of the
two values

```buildoutcfg
url_with_fragment = https://docs.python.org/3/library/urllib.parse.html#module-urllib.parse
defrag = urldefrag(url_with_fragment)
defrag
# DefragResult(url='https://docs.python.org/3/library/urllib.parse.html', fragment='module-urllib.parse')
```


### Quote Functions

The following functions are all about making safe URLs (quoting special characters, encoding non-ASCI text)

#### quote

Replace special characters in the given string. Note that you would usually use this on the path 
section of a given URL, which the `urlsplit()` function can separate for you easily.

```buildoutcfg
escaped_string = quote('á á')
escaped_string
# '%C3%A1%20%C3%A1'
```

#### quote_plus

Like `quote` but also replace spaces.
```buildoutcfg
escaped_string = quote('á')
escaped_string
# '%C3%A1+%C3%A1'
```

#### quote_from_bytes

Like quote, but accepts a bytes object, rather than str.

```buildoutcfg
escaped_bytes = quote_from_bytes(b'a&\xef')
escaped_bytes
# 'a%26%EF'
```

*The above three functions can be reversed with `unqote`, `unquote_plus` and `unquote_to_bytes`*


#### urlencode

Covert either a mapping object (dictionary) or a sequence of two-element tuples to a 
percent-encoded ASCII text string.

```buildoutcfg
data = urllib.parse.urlencode({'type': 'customer foo','client_id': 123})
data
#'client_id=123&type=customer'

```

This whirlwind tour should give you an idea about where you may need to read more. [There are also examples
in the documentation](https://docs.python.org/3/library/urllib.request.html#urllib-examples)





