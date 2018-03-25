---
layout: post
title: Using Python Concurrent Futures
category : Python
tagline: A Basic Guide
tags : [Python, Python Standard Library]
---

# Python `concurrent.futures`

`concurrent futures` are described in [the docs](https://docs.python.org/3/library/concurrent.futures.html) as:
 "a high-level interface for asynchronously executing callables". In this post I'm going to look at:

 - Why you might want to use futures
 - The two key ways to use the futures.Executor `map` method (via threads or processes) and their pros and cons
 - Some useful sample and benchmarking code

If you don't know the difference between a thread and a process, go and Google that, as the below examples assume that knowledge.

### Why use concurrent futures

Concurrent futures provide a simple way to do things in parallel. They were introduced in Python 3.2. Although they have now been backported to Python 2.7, I can't speak to their reliability there and all the examples below are using Python 3.6. Here I'm going to look at `map`, the other method `submit` is a bit more complex, so we'll save that for another day.

Before the introduction of concurrent futures, doing things in parallel in Python would typically require the creation of a somewhat complicated queue and boilerplate. If you Google 'parallelism in Python' and look at older articles, you'll see plenty of these sorts of examples. No more! Also, you may be aware of the `asyncio` module, which has a lot of overlap. I don't want to get into a detailed consideration of the two, but suffice it to say that getting up and running with `concurrent.futures` is much simpler, and requires you to understand fewer concepts than you need to be effective with asyncio (e.g. event loop, coroutines, tasks). 


### Code time!

If you just want to see the code on github, [here it is](https://github.com/ChristopherGS/python_futures_demo).

Let's begin with some simple setup:

- Make sure Python3.6 is installed
- Make sure virtualenv is installed
- Create a directory for you example code and cd in

Create your environment:

`virtualenv --python=={YOUR PYTHON 3.6 PATH} env`


Install requirements:

We're going to use the NLP SpaCy library in some of these examples because I want to.

`pip install spacy` # takes a while

Then load the english language:
`python -m spacy download en`

OK, let's create a file called `futures_demo.py` and setup a basic function, which we will call `transform`. This is the function that we will eventually parallelize. 

```
# futures_demo.py

import concurrent.futures
import time
import os

import spacy
# don't forget to run: python -m spacy download en

nlp = spacy.load('en', parser=False)

INITIAL_SENTENCES = [
	nlp(u'How now brown cow.'),
	nlp(u'Tread softly because you tread on my dreams.'),
	nlp(u'I did not inhale.'),
	nlp(u'But the cops, they pulled me over.'),
	nlp(u'No Mr. Bond, I expect you to die.')
]

def get_tokens(phrase):
	tokens = [token.pos_ for token in phrase]
	return tokens


def transform(phrase):
	print(f'Process {os.getpid()} working on phrase beginning: {phrase[:3]}')
	tokens = get_tokens(phrase)
	print(f'Process {os.getpid()} completed work on beginning: {phrase[:3]}')
	return {phrase: tokens}
```

Now we implement the futures by creating an `Executor`. You can choose to create a `ProcessPoolExecutor` or a `ThreadPoolExecutor`. Let's setup some code to play with both:

```
def run_pool(*, strategy, max_workers=4, sample_text=INITIAL_SENTENCES):
	start = time.time()
	with strategy(max_workers=max_workers) as executor:
		result = executor.map(transform, sample_text)

	end = time.time()

	print(f'\nTime to complete using {strategy}, with {max_workers} workers: {end - start:.2f}s\n')

	print(result) # returns an iterator
	print(list(result))


if __name__=='__main__':
	max_workers = 4
	sample_text = INITIAL_SENTENCES
	# Execution strategy 1: Using processes
	run_pool(strategy=concurrent.futures.ProcessPoolExecutor,
			 max_workers=max_workers,
			 sample_text=sample_text)
	# Execution strategy 2: Using threads
	run_pool(strategy=concurrent.futures.ThreadPoolExecutor,
			 max_workers=max_workers,
			 sample_text=sample_text)
```

Notice that:

- We use the executors in a context manager, to ensure that threads/processes get shutdown.
- We set the `max_worker` argument, which is taken by both Executors, to 4.
- I've put some timing and process ID print statements
- The `map` method expects a function, then an iterable, like the regular Python `map` built-in. 

Go ahead and run this code: `python futures_demo.py`
You should see the sentences and their parts of speech print statements, as well as the following console output:

```
Time to complete using <class 'concurrent.futures.process.ProcessPoolExecutor'>, with 4 workers: 2.62s

[...]

Time to complete using <class 'concurrent.futures.thread.ThreadPoolExecutor'>, with 4 workers: 0.00s
```

You'll also see that when the `ProcessExecutor` is running, there are four different processes running, but during the `ThreadExecutor` there is only one process running.


### Trade offs
#### When to Use the `ThreadPoolExecutor`

As a general rule of thumb (for Python), you want to use threads to if you are looking to parellilize doing heavy IO work, such as HTTP calls, or reading from a file/database.

If, on the other hand, you are doing heavy CPU work, such as heavy number crunching or some kind of complex algorithm, then you want to use processes. Let's see this demonstrated. 

In our example above, we saw that the process pool executor took 2.62s to complete, but that's simply because of the initial overhead of creating a new process. It's a toy example. So let's increase the realism and create a more IO heavy task:

```
def io_intensive_transform(count_list):	
	s = io.StringIO()
	for count in count_list:
		s.write('Hello World{count}\n')

# [...]

def run_pool(*, strategy, max_workers=4, sample_text=INITIAL_SENTENCES):
	start = time.time()
	with strategy(max_workers=max_workers) as executor:
		#result = executor.map(transform, sample_text)
		result = executor.map(io_intensive_transform, range(1,10000))
		#result = executor.map(cpu_intensive_transform, [30, 31, 32, 33, 34, 35])

	end = time.time()

	print(f'\nTime to complete using {strategy}, with {max_workers} workers: {end - start:.2f}s\n')
	print(result) # returns an iterator


if __name__=='__main__':
	max_workers = 4
	sample_text = INITIAL_SENTENCES

	# Execution strategy 1: Using processes
	run_pool(strategy=concurrent.futures.ProcessPoolExecutor,
			 max_workers=max_workers,
			 sample_text=sample_text)
	# Execution strategy 2: Using threads
	run_pool(strategy=concurrent.futures.ThreadPoolExecutor,
			 max_workers=max_workers,
			 sample_text=sample_text)

```

This IO specific test gives the following result:

```
Time to complete using <class 'concurrent.futures.process.ProcessPoolExecutor'>, with 4 workers: 2.85s

<itertools.chain object at 0x123062400>

Time to complete using <class 'concurrent.futures.thread.ThreadPoolExecutor'>, with 4 workers: 0.40s
```

We can see here that the `ThreadPoolExecutor` is faster. If we want to make the test more intense, increase the range passed in to the executor. 

e.g. 
`result = executor.map(io_intensive_transform, range(1,100000))`

Which gives:

```
Time to complete using <class 'concurrent.futures.process.ProcessPoolExecutor'>, with 4 workers: 29.10s

<itertools.chain object at 0x1344cfd30>

Time to complete using <class 'concurrent.futures.thread.ThreadPoolExecutor'>, with 4 workers: 5.35s
```

And you can see that if we increasing the `max_workers` argument can improve performance, although the returns diminish as you increase the number.

#### When to Use the `ProcessPoolExecutor`

Processes are good for CPU intensive stuff. Here's a contrived example:

```
def cpu_intensive_transform(n):
	if n == 0:
		return 0
	elif n == 1:
		return 1
	else:
		return cpu_intensive_transform(n-1) + cpu_intensive_transform(n-2)


def run_pool(*, strategy, max_workers=4, sample_text=INITIAL_SENTENCES):
	start = time.time()
	with strategy(max_workers=max_workers) as executor:
		#result = executor.map(transform, sample_text)
		#result = executor.map(io_intensive_transform, range(1,100000))
		result = executor.map(cpu_intensive_transform, [30, 31, 32, 33, 34, 35])

	end = time.time()

	print(f'\nTime to complete using {strategy}, with {max_workers} workers: {end - start:.2f}s\n')
	print(result) # returns an iterator
```

```
Time to complete using <class 'concurrent.futures.process.ProcessPoolExecutor'>, with 10 workers: 5.98s

<itertools.chain object at 0x1144bea58>

Time to complete using <class 'concurrent.futures.thread.ThreadPoolExecutor'>, with 10 workers: 14.92s
```

As we can see, this time around `ProcessPoolExecutor` performs better. Add more (and larger) numbers to the list past into the `executor.map` to increase the CPU workload. 

---

And that's the easiest way to do parallelism in Python. Pretty amazing stuff.

Here's the [source code](https://github.com/ChristopherGS/python_futures_demo) for the above examples.

