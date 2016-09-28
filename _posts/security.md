POST IS WIP

# Cross-Site Scripting

Recently I was asked to explain Cross-Site Scripting (commonly referred to as XSS), and gave an answer with which I was not satisfied.

In order to improve my understanding, I'm writing this blog post to summarize exactly:

- What XSS is
- Example XSS attacks
- How to defend against XSS

I'm going to really break things down to be as simple as possible. Most definitions of XSS will explain that it is:
> [A] code injection attack that allows an attacker to execute malicious JavaScript in another user's browser.

_OK, so what does that mean?_ First, let's examine "code injection": 

Many websites accept user input, for example when you fill out form information to enter personal details or write comments. This user-generated information
may be temporarily stored on the server, or saved to a database.
If the same website then displays this user generated information somewhere on its pages (i.e. in its HTML), let's say if it has a section called "most hilarious comment of the week", 
then there is a risk. 
The risk comes from the assumption that the user generated content is just text. But a malicious attacker could have entered a malicious script instead of text into 
the form, and if this is displayed in the user's browser, then it will not have the intended effect.

So the "code injection" occurs when the malicious script is inserted stored on the web server/database (where it is passive), and when it is inserted into the 
HTML and served to a user's browser (where it attacks). 

Here is the sequence:

1. An attacker 'injects' a malicious script/command into a website's database by submitting a vulnerable form on the site.
2. The victim visits the website, so their browser requests the page from the web server.
3. The website serves the page with the malicious script/command inside the HTML of the page
4. The victim's browser doesn't know anything is wrong and executes all the Javascript it finds in the HTML - which triggers the malicious
script.  

For those looking at XSS for the first time, one of the most counter-intuitive aspects of it is that it is an attack on the user, not the
application - it is indirect. One could consider the analogy of poisoning people in a restaurant (I've been watching Game of Thrones, sorry).
A cruel assassin secretly puts poison in the wine, and then a waiter unknowingly serves the poisoned wine to the victim. The victim drinks the wine during the course of 
their meal and then suffers. In the context of the web, the attacker "injects" the malicious code into the web application server, fully expecting that the server
will then unknowingly serve the malicious code to a visitor of the site. To continue the analogy, the victim drinking the wine is the website visitor's browser executing
the Javascript of the webpage. Where the analogy breaks down is that a restaurant can only have a limited number of visitors, whereas a website can serve millions of
browser clients.

Clearly the above is an over-simplification, but hopefully it highlights that an XSS attack requires a series of steps in order to work. This diagram shows the steps:

