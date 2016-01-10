---
layout: post
title: Metawear Guide Series Part 3
category : IOT
tagline: Android setup
tags : [IOT, Metawear, tutorial]
---


INCLUDE: importance of logging

*Step 1: Update the repositories closure to include the MbientLab Ivy repository in the project's build.gradle file*


**what the docs say**
```
repositories {
    ivy {
        url "http://ivyrep.mbientlab.com"
        layout "gradle"
    }
}
```

**what it actually looks like**

```
allprojects {
    repositories {
        jcenter()

        ivy {
            url "http://ivyrep.mbientlab.com"
            layout "gradle"
        }
    }
}
```



###why?

*Step 2: Add the compile element to the dependencies closure in the module's build.gradle file.*

**docs**
```
dependencies {
    compile 'com.mbientlab:metawear:2.1.0'
}
```

**my code**

```
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile 'com.android.support:appcompat-v7:22.2.1'
    compile 'com.mbientlab:metawear:2.3.0'
}
```


- In a different file
- multiple dependencies
- API version upgraded to _2.3.0_ as per latest git release

*Step 3: Declare the MetaWearBleService in the module's AndroidManifest.xml file.*

**docs**

```
<application
    android:allowBackup="true"
    android:icon="@drawable/ic_launcher"
    android:label="@string/app_name"
    android:theme="@style/AppTheme" >
        
    <service android:name="com.mbientlab.metawear.MetaWearBleService" />
    <!-- Other application info -->
</application>
```

- this is as per my code
- Not sure if need to enable the bluetooth admin etc. that I currently have


*Step 4: bind the service in your application and retain a reference to the service's LocaBinder class.*

This bit is more complex, let's break it down:

uses these imports:

```
import android.app.Activity;
import android.content.*;
import android.os.Bundle;
import android.os.IBinder;
```

with the crucial one being the `IBinder`

We now begin our function with

```
public class MainActivity extends Activity implements ServiceConnection {
    private MetaWearBleService.LocalBinder serviceBinder;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedIFinally,nstanceState);
        setContentView(R.layout.activity_main);

        // Bind the service when the activity is created
        getApplicationContext().bindService(new Intent(this, 
        MetaWearBleService.class),
                this, Context.BIND_AUTO_CREATE);
    }

...
```

If you have successfully bound to the service, `true` is returned; `false` is returned if the connection is not made so you will not receive the service object.

What is `ServiceConnection`? Defined as:

>[An] Interface for monitoring the state of an application service

OK, so what? 

Well for a start, it means that everything you do with the mbient board needs to be within this function

###Is that true?
