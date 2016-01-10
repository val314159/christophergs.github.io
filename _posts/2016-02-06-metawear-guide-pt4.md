---
layout: post
title: Metawear Guide Series Part 4
category : IOT
tagline: Connecting to the Board
tags : [IOT, Metawear, tutorial]
---




**Step 4: Accessing the Board**

####Option 1: Copy mbient app i.e. Bluetooth scan (more complex

- Note the critical importance of adding

`compile 'com.mbientlab.bletoolbox:scanner:0.2.0'`
to the module gradle file


- works quite well if you comment out the navigation intent
- need to create scanner.xml and layout
- need to update strings

+> use the progress dialog in your app - useful!


####Option 2: Use the board MAC address to access it (can find this out using the MetaWear Android app)

`import com.mbientlab.metawear.module.Bmi160Gyro.*;`

needed for configuring:

```
bmi160GyroModule.configure()
                            .setFullScaleRange(FullScaleRange.FSR_2000)
                            .setOutputDataRate(OutputDataRate.ODR_100_HZ)
                            .commit();
```

http://community.mbientlab.com/discussion/370/logging-acceleration-offline-n00b-question 
It is possible you are starting the accelerometer before the other components are enabled.  Once the accelerometer is in active mode, all configurations are ignored.  Routes are added in order they are committed so you can have the last async operation start the accelerometer.

http://community.mbientlab.com/discussion/424/downloading-logs


update the firmware!


https://mbientlab.com/gettingstarted/
"The Android Emulator does not support bluetooth, therefore will not run the device"

"Change the main activity class to implement the service connection interface"


"don't forget to unbind the service when the activity is destroyed"

press `alt` + `Enter` to have Android Studio auto-generate the required imports and interface functions

Add a filter on the log tag - good for part 2!

NEED TO CHECK WHICH BOARDS HAVE WHICH ACCELEROMETERS\

12.5f outputDataRate for accelerometer should "not flood bluetooth radio"

50f "more data"

get.module methods may return a checked exception, so you must wrap them in a `try` `catch` block

debug.module.resetDevice() soft reset of device, 

MUST FIRST enableaxissampliing()
then record

to stop, first stop then disable axissampling()


I SHOULD DO THE AVERAGING ON THE BOARD!!! using data processing.


QUESTION: Can you stream and log at the same time?


To save a csv
`<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />`

to the *AndroidManifest.xml*



