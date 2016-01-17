---
layout: post
title: MetaWear Guide Series Part 2
category : IOT
tagline: Prerequisites and initial preparation
tags : [IOT, Metawear, tutorial]
---

###Prerequisites

1) You will need at least one MetaWear sensor, which you can buy from [mbientlabs](https://mbientlab.com/metawear/). The code in this series should work for any of the sensors, but I have only tested it on:

- MetaWear R
- MetaWear C

2) You will need an Android phone (running on a minimum of Android 4.3) Unfortunately, at the time of writing the Android Emulator does not support bluetooth, which is required for working with the board.

3) You should download and install the (official MetaWear app)[https://play.google.com/store/apps/details?id=com.mbientlab.metawear.app&hl=en] on your phone. Once you have downloaded the app, use it to make sure that you have the latest firmware installed but clicking the "Update Firmware" button. We will also refer back to this app in the tutorial. 

The are also a number of software requirements, let's tackle them here:   


**Step 1: Basic Android Setup**

As per the [google Android docs](http://developer.android.com/training/index.html) the first thing to do is [download and install Android Studio](http://developer.android.com/sdk/installing/index.html?pkg=studio), which gives you the tools you need. It's a big old download, so allow some time. Note that Android Studio has a few dependencies, such as the Java Development Toolkit (JDK) version 6 or higher. These are detailed on the Android Studio download page. You may prefer to use other IDEs like Eclipse, but for the purposes of this series, we will refer to Android Studio. 

Start up Android Studio, and on the first screen select "Start a new Android Studio project":

![start new Android Studio Project]({{site.baseurl}}/assets/images/metawear/metawear_ss_1.jpg)


The [google Android docs](http://developer.android.com/training/basics/firstapp/creating-project.html) explain the next screen clearly. For this tutorial I am calling my Application "MetawearGuide", company name is my github (but could be anything):

![Project details]({{site.baseurl}}/assets/images/metawear/metawear_ss_2.jpg)

Note also the project location so you can find it later. Hit "Next".

On this screen, it is important to set the correct Android version. As per the MetaWear docs:

> A minimum of Android 4.3 (SDK 18) is required to use this library however some features will not properly function due to the underlying Bluetooth LE implementation. For the best results, it is recommended that users be on Android 4.4 (SDK 19) or higher.

With this in mind, I select "API 19: Android 4.4 (KitKat)" from the dropdown menu:

![API Version]({{site.baseurl}}/assets/images/metawear/metawear_ss_3.jpg)

We leave the other options (Wear, TV, Android Auto) unchecked and click "Next".

On the "Add an activity" screen, select "Blank Activity" and click "Next":

![Select Blank Activity]({{site.baseurl}}/assets/images/metawear/metawear_ss_4.jpg)

Now, to keep the terminology the same as the Google tutorial, on the final screen we will:

- Under "Customize the Activity", change the Activity Name to "MyActivity". 
- Set Layout Name to "activity_my"
- Set Title to "MyActivity"
- Set the Menu Resource Name as "menu_my"

Click "Finish". We've now created our basic Android Application.   



**Step 2: Mobile Setup**

Now, we have a default "Hello World" app ready to run in Android Studio. Let's test it. If this is the first time you are using your phone for development, you will need to [enable developer options](http://www.greenbot.com/article/2457986/how-to-enable-developer-options-on-your-android-phone-or-tablet.html). Connect your Android phone to your laptop using a normal USB to USB-micro cable. 

In Android Studio, select "Run" (the button with a green play symbol). It will take a little time while the code compiles. You should then see a popup screen to choose a running device:

[ADD IMAGE]

Be sure to select your actual phone, rather than using an emulator. Once you select your device, the app will be copied onto your phone, and then open automatically. You see the "Hello World" app open on your phone. If you have any problems, be sure to review the Google Tutorial, which is very clear.


**Step 3: Metawear Setup**

The Metawear API setup instructions say:

*To add the library to your project, first, update the repositories closure to include the MbientLab Ivy Repo in the project's build.gradle file.*

```
repositories {
    ivy {
        url "http://ivyrep.mbientlab.com"
        layout "gradle"
    }
}
```

and also say *Then, add the compile element to the dependencies closure in the module's build.gradle file.*

```
dependencies {
    compile 'com.mbientlab:metawear:2.3.0'
}
```

For those new to Android, this can be confusing, because you will find that you have two `build.gradle` files. To be clear: 

- A project in Android Studio represents a complete Android app. Android Studio projects consist of one or more modules.
- A module is a component of your app that you can build, test, or debug independently.

So for our MetawearGuide project, we update *build.gradle(Project: Metawear Guide)* like so:

{% highlight java %}

//...previous code
allprojects {
    repositories {
        jcenter()
        ivy { //added
            url "http://ivyrep.mbientlab.com"
            layout "gradle"
        }
    }
}
{% endhighlight %}

and then our *build.gradle(Module: app)* like so:

{% highlight java %}
//...previous code...
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:22.2.1'
    compile 'com.mbientlab:metawear:2.3.0' //added: NOTE version 2.3.0, the docs show an older version
}
{% endhighlight %}

We now need to associate the source code with the AAR package (an 'aar' bundle is the binary distribution of an Android Library Project). To do this we switch the project view to display the project structure:

![Select Blank Activity]({{site.baseurl}}/assets/images/metawear/metawear_ss_5.jpg)

- Expand the "External Libraries" tab
- Right click "metawear-${version}" and select "Library Properties":

![](/content/images/2015/12/metawear_ss_6.png)

- Press the add button (green plus icon) and select "sources.jar" under the "jar" tab:

![](/content/images/2015/12/metawear_ss_7.png)

Make sure that you sync with the updated Gradle files, then open the *AndroidManifest.xml* file and add the following line:
```
<application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:theme="@style/AppTheme" >
        
        <service android:name="com.mbientlab.metawear.MetaWearBleService" /> 
```

This declares the MetaWear Bluetooth LE service in the module.

One other key setup point which is not obviously mentioned in the docs is that you need to set permissions for Bluetooth admin. To do this, still in the *AndroidManifest.xml* file, add the following line at the top:

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.mbientlab.metawear.app" >

    <uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
//...file continues...
```

The final part in the basic setup is the most complicated: Binding the service.

>Lastly, bind the service in your application and retrain a reference to the service's LocalBinder class. This can be done in any activity or fragment that needs access to a MetaWearBoard object.

In our *MyActivity.java* file, we will set this up.

First, add the necessary imports:
```
import android.app.Activity;
import android.content.*;
import android.os.Bundle; //usually in MyActivity by default
import android.os.IBinder;

import com.mbientlab.metawear.MetaWearBleService;
```
Add the following the `MainActivity` Class:

```
public class MyActivity extends AppCompatActivity implements ServiceConnection {
    private MetaWearBleService.LocalBinder serviceBinder;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_my);

        ///< Bind the service when the activity is created
        getApplicationContext().bindService(new Intent(this, MetaWearBleService.class),
                this, Context.BIND_AUTO_CREATE);
    }

//...Code continues...
```

Note how the `MyActivity` class now implements the service connection. Finally we add the the following methods to the class:

```
//...Previous code...
@Override
    public void onDestroy() {
        super.onDestroy();

        ///< Unbind the service when the activity is destroyed
        getApplicationContext().unbindService(this);
    }

    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        ///< Typecast the binder to the service's LocalBinder class
        serviceBinder = (MetaWearBleService.LocalBinder) service;
    }

    @Override
    public void onServiceDisconnected(ComponentName componentName) { }

//...Default code continues...
```



At this point we have done our basic Metawear setup...but there's not much to see! Let's start digging deeper into the API.

*Note* You can view all these changes in the [github repository](https://github.com/ChristopherGS/MetawearGuide) on the branch **version-0.1**
