---
layout: post
title: An Overview of Cross-Site Scripting
category : security
tagline: 
tags : [security, web development]
---


## Introduction

Recently I was asked to explain Cross-Site Scripting (commonly referred to as XSS), and gave an answer with which I was not satisfied.

In order to improve my understanding, I'm writing this blog post to summarize exactly:

- What XSS is
- Example XSS attacks
- How to defend against XSS

_Note that I am not an expert in this area, so make sure you do further fact checking to secure your website!_

I'm going to really break things down to be as clear as possible. For those looking at XSS for the first time, one of the most counter-intuitive aspects of it is that it is an attack on the user, not the
application - it is indirect.

Let's begin with an analogy:

An assassin wishes to poison people in a restaurant (I've been watching _Game of Thrones_, sorry). The assassin secretly puts poison in the restaurant's wine, and then a waiter unknowingly serves the poisoned wine to the victim(s). The victim(s) drinks the wine during the course of their meal and then suffers. 

In the context of the web, the attacker "injects" the malicious code into the web application server, fully expecting that the server
will then unknowingly serve the malicious code to a visitor of the site. To continue the analogy, the victim drinking the wine is the website visitor's browser executing the Javascript of the webpage. Where the analogy breaks down is that a restaurant can only have a limited number of visitors, whereas a website can serve millions.

Clearly, the analogy is a bit a of a stretch, but hopefully it highlights that an XSS attack requires a series of steps in order to work. Now let's get into some more detail...


## Understanding XSS

Most definitions of XSS will explain that it is:

> "[A] code injection attack that allows an attacker to execute malicious JavaScript in another user's browser."

_OK, so what does that mean?_ First, let's examine "code injection": 

Many websites accept user input, for example when you fill out form information to enter personal details or write comments. This user-generated information may be temporarily stored on a web server, or saved to a database. If the same website then displays this user generated information somewhere on its pages (i.e. in its HTML), then there is a risk. The risk comes from the assumption that the user generated content is just text. But an attacker could have entered a malicious script instead of text into the form, and if this is displayed in the user's browser, then it could be damaging.

So the "code injection" occurs when the malicious script is inserted into the web server/database (where it is passive), and when it is inserted into the HTML and served to a user's browser (where it attacks). 

***

### The Series of Events for XSS


1. An attacker 'injects' a malicious script/command into a website's database by submitting a vulnerable form on the site.
2. The victim visits the website, so their browser requests the page from the web server.
3. The website serves the page with the malicious script/command inside the HTML of the page
4. The victim's browser doesn't know anything is wrong and executes all the Javascript it finds in the HTML - which triggers the malicious
script.  

Here is a diagram of the process - [adapted from this article](https://www.acunetix.com/websitesecurity/cross-site-scripting/)

![XSS Diagram]({{site.baseurl}}/assets/images/xss_diagram_small.jpg)

***

## Possible XSS Attacks

So what is "malicious Javascript"? Isn't Javascript confined to the browser - so like, isn't it pretty harmless? Well, you'd be surprised at how much trouble Javascript can cause:

- Any malicious Javascript has access to the web page's objects - which means it can access cookies. Since cookies are frequently used to store session tokens, then the malicious 
Javascript can use the session cookie to impersonate that user.
- Javascript can use `XMLHttpRequest` to send HTTP requests to other destinations (for example, sending your cookie data to a malicious web server).
- In newer browsers, Javascript can make use of HTML5 APIs - which means access to geolocation, webcams, and microphones.
- Of course, Javascript can manipulate the user's DOM, so it can remove/add new sections to a webpage.

A complex XSS attack could combine any of the above possibilities - which could lead to some very serious risks.

Malicious scripts may be written in any number of scripting languages, provided that the client host (i.e. the browser) can interpret the code. 
Scripting tags that are most often used to embed malicious content include `<SCRIPT>`, `<OBJECT>`, `<APPLET>` and `<EMBED>`.
Most information about XSS involves dealing with `<SCRIPT>` tags, but other HTML tags can also be used for nefarious purposes.
Consider the `<FORM>` HTML element – by inserting a new form, an attacker could trick 
visitors to the site into revealing sensitive information by adding new information fields, or modifying the behaviour of an existing form. 
Other HTML tags may be inserted to alter the appearance and behaviour of a page - potentially discrediting the website. This could take the form of something subtle, such as adjusting annual accounts, or something extreme such as porn pop-ups.

Hopefully the above points make it clear that XSS is no joke. So how do we defend against it?

***

## Defending Against XSS Attacks

There are two main varieties of XSS vulnerabilities we need to consider when planning our defenses:

1. _Stored XSS_ refers to when data a user submits to a website is persisted (on disk or in RAM) across requests. This means that victims don't have to take any 
action other than visiting the site in order to be attacked. For this reason, a stored XSS is generally considered a more dangerous vulnerability.

2. _Reflective XSS_ occurs when a particular page can be used to execute arbitrary code, but it does not persist the attack code across multiple requests. 
Since an attacker needs to send a user to a specially crafted URL for the code to run, reflective XSS usually requires some kind of email trickery ("social engineering")
to convince people to fall into the trap. For this reason, it is less of a threat (though still needs to be taken seriously).

Here are a few key defensive measures one should employ to reduce the liklihood of XSS vulnerability:

#### 1. Input validation
Any user input that gets sent to your web server should be checked (on both the browser and the server). Validation works best by preventing XSS attacks on data which has inherent value limits. An integer, for example, should never contain HTML special characters; An option, such as a country name, should match a list of allowed countries which likewise will prevent XSS payloads from being injected. It is important to consider what values make sense for the given context.

#### 2. HTML Escaping
The most common security advice is to focus on those areas where you have to put untrusted data into your HTML, and to properly "escape" that HTML so that it cannot include arbitrary HTML tags. This is also referred to as contextual filtering. What does it mean to "escape" text? For example, `<script>` gets converted to `&lt;script&gt;` which prevents any code execution. This conversion is "entity encoding", so you will sometimes see escaping/encoding used interchangeably. The two are actually different things - [here is a discussion of their differences](http://stackoverflow.com/questions/763294/xml-escaping-encoding-terminology)

[Here is an example of the escaping process written in Python](http://code.activestate.com/recipes/496942/)

If you are using a popular web framework (Django, express etc.) and/or a template engine like Jinja2 there is a good chance that the template engine or the framework will help with this process. However, even if HTML is automatically escaped by your framework of choice, understanding context is very important. [The Django docs](https://docs.djangoproject.com/en/1.10/topics/security/) provide this example when discussing XSS:

{% raw %}
`<style class={{ var }}>...</style>`
{% endraw %}

>If var is set to 'class1 onmouseover=javascript:func()', this can result in unauthorized JavaScript execution

{% raw %}
Even though `{{ var }}` is escaped by Django templates automatically, it does not guarantee security.
{% endraw %}

#### 3. Quote HTML Attributes

If an XSS attack involves injecting new attributes onto existing HTML elements, then simple escaping will not be enough protection. 
To prevent this sort of attack, quote your attributes with either double or single quotes. For example (with Jinja templates):
{% raw %}
`<a href="{{ href }}">the text</a>`
{% endraw %}

This prevents an attacker from injecting Javascript into the attribute.

#### 4. Always Javascript Escape Before Injecting Data Into Javascript Data Values
Javascript data values are basically strings. Since you can’t escape numbers, there is a sub-rule you can apply:

Always Validate Numbers.

***


This brief list is not exhaustive, but hopefully will alert the reader to some of the most obvious ways they can improve their website
security. 
For a more comprehensive XSS prevention guide, see the [OWASP XSS Prevention Cheat Sheet](https://www.owasp.org/index.php/XSS_Prevention_Cheat_Sheet#Introduction)






