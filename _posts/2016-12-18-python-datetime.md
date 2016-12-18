---
layout: post
title: Using Python datetime
category : Python
tagline: A Basic Guide
tags : [Python, Python Standard Library, web development]
---

# Python `datetime`

The `datetime` module is for manipulating dates and times. It is often used in conjunction
with the [`time`](https://docs.python.org/3/library/time.html#module-time) and [`calendar`](https://docs.python.org/3/library/calendar.html#module-calendar) modules. In programming, dealing with dates
and times can be difficult. In a Python context, key concepts to note are that we usually deal with two types of date and time objects:

1. "Aware": knows about political time adjustments (e.g. daylight saving), and therefore
can locate itself in a specific moment in time. Not open to interpretation.
1. "Naive": Does not know about political time adjustments, and as such is open to interpretation
by the program.

If you need an "aware" object, `datetime` and `time` objects have an optional attribute, `tzinfo`, which is used for
capturing timezone-related information.


### Key Types

`date`: A naive date. Attributes: year, month, day.

```
import datetime

my_date = datetime.date(year=2000, month=12, day=25)
print(my_date)
# 2000-12-25
```

`time`: An idealized time, without leap seconds or anything fancy. Attributes: hour, minute, second, microsecond, tzinfo

```
import datetime

my_time = datetime.time(hour=5, minute=30, second=30)
print(my_time)
# 05:30:30
```

`datetime`: A combination of `date` and `time`

```
import datetime

dt = datetime.datetime(year=2000, month=12, day=25, hour=5, minute=30, second=30)
print(dt)
# 2000-12-25 05:30:30
```


`timedelta`

A duration created whenever expressing the difference between *two* of the following: `date`, `time`, `datetime`

```
import datetime

dt = datetime.datetime(year=2000, month=12, day=25, hour=5, minute=30, second=30)
dt2 = datetime.datetime(year=2000, month=12, day=26, hour=5, minute=30, second=30)
difference = dt2 - dt
print(type(difference))
# <class 'datetime.timedelta'>
print(difference.total_seconds())
# 86400.0
```

### Common Operations

To get the current datetime:

```
import datetime
foo = datetime.datetime.today()
bar = datetime.datetime.now()

print(foo)
# 2016-12-18 19:47:15.132553
print(bar)
# same as above. However, you could have specified the tzinfo

```

When you first use `datetime`, you are likely to spend a while messing around with `strftime` and `strptime`. It's
worth understanding exactly what these are doing to reduce wasted effort.

All `date`, `time` and `datetime` support a `strftime()` method. This creates a string representation of the given object with an explicit format (that you specify). The format codes are a little counter intuitive, and it is [well worth reviewing them](http://strftime.org/) to avoid long debugging sessions

Conversely, the `strptime()` method creates a `datetime` object from a string representation of a date and time, with the format passed in to help the method understand the string.

```
import datetime

new_dt = datetime.datetime.strptime('2000-12-25:19:30:45', '%Y-%m-%d:%H:%M:%S')
print(new_dt)
# 2000-12-25 19:30:45

proper_dt = datetime.datetime.strftime(new_dt, '%Y-%m-%d')
print(proper_dt)
# 2000-12-25
```

When you start including the `tzinfo` attribute in your `datetime` objects, thing grow more complex, as you are now creating "aware" objects. Be sure to [read the docs](https://docs.python.org/3/library/datetime.html#tzinfo-objects) if you need "aware" objects, as there are many gotchas. A popular module to help with these calculations is [`pytz`](http://pytz.sourceforge.net/).

You are now ready to do basic time and date manipulations in your Python applications.