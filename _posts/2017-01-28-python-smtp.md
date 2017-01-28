---
layout: post
title: Using Python smtplib and smptd
category : Python
tagline: A Basic Guide
tags : [Python, Python Standard Library, web development]
---

# Sending Emails with Python: smptlib and smtpd 

### Introduction

This is a basic guide for an area where I wasn't able to find any particularly great tutorials,
perhaps because this stuff is obvious to more experienced developers. Well, it wasn't to me.

OK, to begin: If you want to send an email using Python, you need two things:

1) An SMTP client

2) An SMTP server

In most cases, you will connect to a remote SMTP server, such as gmail's, or perhaps your company's
SMTP server. However, it is also possible to create your own. In this post I will explain both 
cases.


### Creating the SMTP Client with smtplib

For basic use of [smtplib](https://docs.python.org/2/library/smtplib.html), you need to 
create an SMTP instance, which initializes the connection to an SMTP server, then you can use
the `sendmail()` and `quit()` methods.

e.g.

```buildoutcfg
import smtplib

client = smtplib.SMTP('localhost')

fromaddr = 'your_email@somedomain.com'
toaddrs = 'your_friend@somedomain.com'
msg = 'Hello'

client.sendmail(fromaddr, toaddrs, msg)
client.quit()
```

If you run this code locally and do not have an SMTP server running, you will get a connection
failed error. On Linux, the easiest thing to do is to install something like [Postfix](http://www.postfix.org/SMTPD_ACCESS_README.html)
which creates an SMTP server for your. Once you setup and start that server, the above code will
work. Alternatively, you could connect to an external service (such as gmail), and in that case your code
will change to look like this:

```buildoutcfg
def send_email(user, pwd, recipient, subject, body):
    import smtplib

    gmail_user = user
    gmail_pwd = pwd
    FROM = user
    TO = recipient if type(recipient) is list else [recipient]
    SUBJECT = subject
    TEXT = body

    # Prepare actual message
    message = """From: %s\nTo: %s\nSubject: %s\n\n%s
    """ % (FROM, ", ".join(TO), SUBJECT, TEXT)
    try:
        server = smtplib.SMTP("smtp.gmail.com", 587)
        server.ehlo()
        server.starttls()
        server.login(gmail_user, gmail_pwd)
        server.sendmail(FROM, TO, message)
        server.close()
        print 'successfully sent the mail'
    except:
        print "failed to send mail"
```

Note that when connecting to external services, we need to employ more authentication methods
such as [`ehlo()`](https://docs.python.org/2/library/smtplib.html#smtplib.SMTP.ehlo) and `login()`

### Setting up your own SMTP Server with smtpd

If you want to do everything yourself, you can setup your own SMTP server using [smtpd](https://docs.python.org/2/library/smtpd.html)

```buildoutcfg
import smtpd
import asyncore

# expects a pair of tuples, passing in None for the second
server = smtpd.SMTPServer(('localhost', 1025), None)

asyncore.loop()
```

The `smtpd.SMTP` class inherits from [`asyncore dispatcher`](https://docs.python.org/2/library/asyncore.html#asyncore.dispatcher)
meaning that the server is created when we call `asyncore.loop()`

A gotcha I found was that the `asycore.loop()` method enters a polling loop that then blocks any
subsequent code. The solution to this is to either place your SMTP server code in a separate file
and run it via a separate terminal, or to run it in its own thread.

### Bringing it Together

Here's a complete working example, combining both libraries

```buildoutcfg
import threading
import smtpd
import asyncore
import smtplib

server = smtpd.SMTPServer(('localhost', 1025), None)

loop_thread = threading.Thread(target=asyncore.loop, name="Asyncore Loop")
# If you want to make the thread a daemon
# loop_thread.daemon = True
loop_thread.start()

# port should match your SMTP server
client = smtplib.SMTP('localhost', port=1025)

fromaddr = 'your_email@gmail.com'
toaddrs = 'your_friend@gmail.com'
msg = 'Hello'

server.sendmail(fromaddr, toaddrs, msg)
server.quit()
```

Note that if you try and run this code using your gmail/outlook etc. email addresses, you will
get permission denied errors because the login process is required.

