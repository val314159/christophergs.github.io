---
layout: post
title: Metawear Guide Series Part 2
category : IOT
tagline: Prerequisites and initial preparation
tags : [IOT, Metawear, tutorial]
---

###Prerequisites




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
