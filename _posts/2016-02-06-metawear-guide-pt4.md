---
layout: post
title: Metawear Guide Series Part 4
category : IOT
tagline: Using Board Modules
tags : [IOT, Metawear, tutorial]
---

*This is part 4 of a multipart series showing you how to get started with the MetaWear platform. [View the contents of the series to easily skip forwards or backwards]({{ site.baseurl }}/iot/2016/01/17/metawear-guide-pt1/)*

####About Modules

Now that we have established a connection to the board, we can begin interacting with modules. 

>Modules are on-board sensors or features supported by the firmware.

We do this by calling `MetaWearBoard.getModule`.

Let's start with a simple example, allowing us to turn on the LED lights on the board.

First, we'll add two buttons underneath our *connect* button: *LED on* and *LED off*. Add the following to *activity_my.xml*:

{% highlight java %}
<Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="LED on"
        android:id="@+id/led_on"
        android:layout_below="@+id/connect"
        android:layout_centerHorizontal="true"
        android:layout_marginTop="58dp" />

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="LED off"
        android:id="@+id/led_off"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true" />

{% endhighlight %}

<br>

Then in our *MyActivity.java* file let's import the LED module and declare the object. We'll also import `UnsupportedModuleException` for our error handling:

{% highlight java %}

//other imports

import com.mbientlab.metawear.UnsupportedModuleException;
import com.mbientlab.metawear.module.Led;

public class MyActivity extends AppCompatActivity implements ServiceConnection {

    //CONSTANTS
    private final String MW_MAC_ADDRESS= "D5:9C:DC:37:BA:AE"; //update with your board's MAC address
    private static final String TAG = "MetaWear";
    private Button connect;
    private Button led_on;
    private Button led_off;

    //METAWEAR OBJECTS
    private MetaWearBleService.LocalBinder serviceBinder;
    private Led ledModule; //Declare the ledModule
    private MetaWearBoard mwBoard;

    //rest of code
{% endhighlight %}

<br>

Then inside the `connected` section of our `ConnectionStateHandler` we define the module using `getModule`. Note that this is wrapped in a `try/catch` statement. If we tried defining the ledModule somewhere else, such as in `onCreate`, it would cause an error as at that point board connection handling may not be complete:

{% highlight java %}
private final ConnectionStateHandler stateHandler= new ConnectionStateHandler() {
        @Override
        public void connected() {
            Log.i(TAG, "Connected");
            try {
                ledModule = mwBoard.getModule(Led.class);
            } catch (UnsupportedModuleException e) {
                e.printStackTrace();
            }
        }
{% endhighlight %}

<br>

The last thing we need to do is add the `onClick` handlers, also within the `connected()` code block. To quote [the docs](https://mbientlab.com/androiddocs/#led){:target="_blank"}: 

>LED patterns function as a pulse with four main parameters to modify: rise time, high time, fall time, and duration. These parameters are modified using the ColorChannelEditor and each color channel (rgb) is configured independent of the other colors allowing you to program different patterns for each color.

So we use `ColorChannelEditor` inside our `onClick` and then commit the result first, then we play them:

{% highlight java %}
led_on=(Button)findViewById(R.id.led_on);
            led_on.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Log.i(TAG, "Turn on LED");
                    ledModule.configureColorChannel(Led.ColorChannel.BLUE)
                            .setRiseTime((short) 0).setPulseDuration((short) 1000)
                            .setRepeatCount((byte) -1).setHighTime((short) 500)
                            .setHighIntensity((byte) 16).setLowIntensity((byte) 16)
                            .commit();
                    ledModule.play(true);
                }
            });
{% endhighlight %}

To turn the LED off, it's just one line of code within our second `onClick`:

{% highlight java %}
led_off=(Button)findViewById(R.id.led_off);
            led_off.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                    Log.i(TAG, "Turn off LED");
                    ledModule.stop(true);
                }
            });
{% endhighlight %}

Go ahead and run the app - you should see the LED turn on and off when you press the *LED on* and *LED off* buttons (make sure you connect to the board first!). Now we're really making progress. Spend some time playing with different settings for the LED duration, intensity and color. 

In the next section we will look at streaming data.

*Note* You can view all these changes in the [github repository](https://github.com/ChristopherGS/MetaWearGuide/tree/version-0.3){:target="_blank"} on the branch **version-0.3**

[Next post in the series -->]({{ site.baseurl }}/iot/2016/02/07/metawear-guide-pt5/)
