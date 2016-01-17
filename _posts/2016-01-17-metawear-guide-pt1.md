---
layout: post
category : IOT
title: MetaWear Guide Series Part 1
tagline: "Why use MetaWear?"
tags : [IOT, Metawear, tutorial]
---

###Introduction

This is the first in a series of tutorial posts designed to help you start working with the Metawear platform using the Android API. The series is designed to be followed in order, with the complexity of posts (and the sample project) gradually growing as the series unfolds. We will gradually build a demo project, a wearable app which is able to detect push ups.

*Some prior knowledge of programming is required, but I've tried to make this as accessible as possible, so no familiarity with Android is assumed. Note that I am not affiliated with the Mbient Labs team, so there may be mistakes. Pull requests are welcome!*

Here is the full series

1) Why use MetaWear? (This Post)

2) [Prerequisites and initial setup]({{ site.baseurl }}/iot/2016/01/24/metawear-guide-pt2/)

3) [Android setup]({{ site.baseurl }}/iot/2016/01/31/metawear-guide-pt3/)

4) [Connecting to your board]({{ site.baseurl }}/iot/2016/02/06/metawear-guide-pt4/)

5) Interacting with the board (Streaming) 

6) Dealing with data (Logging)

8) Multiple sensors

9) Saving our data to CSV

10) Sending our data over HTTP

----------------------------
THIS IS WHERE I AM UP TO

10) A better connection

11) Capturing data for longer time durations

12) Processing on the board



## Why MetaWear?


MetaWear is made by [mbient labs](https://mbientlab.com/metawear/), and they give describe their reasons for creating the platform like this:

>The MetaWear platform provides a one-stop shop for wearable sensor solutions. Our goal is to completely eliminate the need for product developers to build hardware or write firmware code.

This is good news. MetaWear provides a great way to prototype and develop Internet of Things (IoT) ideas. You can put Metawear sensors on clothing, vehicles, buildings and many more, and then analyze the data that the sensors send back to you. What exactly you do with this data is up to you, but Metawear allows you to focus on the interpretation part, and takes care of the hardware. This is a big time saver, until recently you would have had to have written a lot of complex firmware, probably in C or C++. 

The technology is affordable (a typical sensor is less than $50), and there are well documented APIs for Android and iOS. For me, the Metawear sensors have been a great way to experiment with wearable technology ideas. This post series will show you how to get started and make good use of the technology.

[Next post in the series -->]({{ site.baseurl }}/iot/2016/01/24/metawear-guide-pt2/)




