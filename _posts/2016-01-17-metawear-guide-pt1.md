---
layout: post
category : IOT
title: MetaWear Guide Series Part 1
tagline: "Why use MetaWear?"
tags : [IOT, Metawear, tutorial, guide]
---

## Introduction

This is the first in a series of tutorial posts which aim to help you start working with the Metawear platform using the Android API. The series is designed to be followed in order, with the complexity of posts (and the sample project) gradually growing as the series unfolds.

Some prior knowledge of programming is required, but I've tried to make this as accessible as possible, so no familiarity with Android is assumed. There is quite a big jump from the (excellent) [documentation](https://mbientlab.com/androiddocs/){:target="_blank"} to the [sample Android app](https://github.com/mbientlab/Metawear-SampleAndroidApp){:target="_blank"}, and this series aims to bridge that gap. Note that I am not affiliated with the MbientLab team, so there may be mistakes. Pull requests are welcome!

Here is the full series

1) [Why use MetaWear? (This Post)]({{ site.baseurl }}/iot/2016/01/17/metawear-guide-pt1/)

2) [Prerequisites and Initial Setup]({{ site.baseurl }}/iot/2016/01/24/metawear-guide-pt2/)

3) [Connecting to your Board]({{ site.baseurl }}/iot/2016/01/31/metawear-guide-pt3/)

4) [Using Board Modules]({{ site.baseurl }}/iot/2016/02/06/metawear-guide-pt4/)

5) [Streaming Data]({{ site.baseurl }}/iot/2016/02/07/metawear-guide-pt5/)

6) [Logging Data]({{ site.baseurl }}/iot/2016/02/22/metawear-guide-pt6/)

7) [Multiple Streams]({{ site.baseurl }}/iot/2016/02/26/metawear-guide-pt7/)

8) [Saving our Data to CSV]({{ site.baseurl }}/iot/2016/02/28/metawear-guide-pt8/)

9) [A Better Connection I]({{ site.baseurl }}/iot/2016/02/28/metawear-guide-pt9/)

10) [A Better Connection II]({{ site.baseurl }}/iot/2016/02/29/metawear-guide-pt10/)

Every step has corresponding [code on github](https://github.com/ChristopherGS/MetawearGuide) to check against, marked with different branches. Somewhat confusingly, branch versions are one step behind post numbers.

<br>
*Ideas for future posts*

- Reconnection logic
- Graphing our data
- Pre-Processing on the board
- Working with more than one board



<br>

## Why MetaWear?


MetaWear is made by [mbientlab](https://mbientlab.com/metawear/){:target="_blank"}, and they describe their reason for creating the platform like this:

>The MetaWear platform provides a one-stop shop for wearable sensor solutions. Our goal is to completely eliminate the need for product developers to build hardware or write firmware code.

This is good news. MetaWear provides a great way to prototype and develop Internet of Things (IoT) ideas. You can put MetaWear sensors inside clothing, vehicles, buildings and many more, and then analyze the data that the sensors send back to you. What exactly you do with this data is up to you, but MetaWear allows you to focus on the interpretation part, and takes care of the hardware. This is a big time saver, until recently you would have had to have written a lot of complex firmware, probably in C or C++. 

The technology is affordable (a typical sensor is less than $50), and there are well documented APIs for Android and iOS. For me, the MetaWear sensors have been a great way to experiment with wearable technology ideas. This series of tutorial posts will show you how to get started and make good use of the technology.

[Next post in the series -->]({{ site.baseurl }}/iot/2016/01/24/metawear-guide-pt2/)




