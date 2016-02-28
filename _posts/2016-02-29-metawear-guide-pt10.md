---
layout: post
title: MetaWear Guide Series Part 10
category : IOT
tagline: A Better Connection II
tags : [IOT, Metawear, tutorial]
---

*This is part 10 of a multipart series showing you how to get started with the MetaWear platform. [View the contents of the series to easily skip forwards or backwards]({{ site.baseurl }}/iot/2016/01/17/metawear-guide-pt1/)*

To make our *ScannerActivity* useful, we need to be able to trigger our *MyActivity* class (which we built in parts 2-8). We will do this via the [mechanism of an Android intent](http://developer.android.com/intl/zh-tw/reference/android/content/Intent.html), which is how you start one activity from within a different activity.

Within our MetaWear board's `connected` state handler code, we add the following lines:

{% highlight java %}
mwBoard.setConnectionStateHandler(new MetaWearBoard.ConnectionStateHandler() {
@Override
public void connected() {
    connectDialog.dismiss();
    Log.i(TAG, String.format("MAC Address: %s", mwBoard.getMacAddress()));
    Intent mainActivityIntent = new Intent(ScannerActivity.this, MyActivity.class);
    mainActivityIntent.putExtra(MyActivity.EXTRA_BT_DEVICE, btDevice);
    ScannerActivity.this.startActivity(mainActivityIntent);
}
{% endhighlight %}

In the above code block, we trigger the new intent and pass our board details using the `putExtra` method.

We will need to have some corresponding code in our *MyActivity* class for this to work. So in *MyActivity*, first, add a few variables:

{% highlight java %}
public static final String EXTRA_BT_DEVICE = "com.christophergs.metawearguide.EXTRA_BT_DEVICE";
private BluetoothDevice btDevice;
{% endhighlight %}


Next, our `onCreate` method, we add the reference to the data that was passed from *ScannerActivity*, i.e. the reference to the board:

`btDevice= getIntent().getParcelableExtra(EXTRA_BT_DEVICE);`


We are no longer going to rely on a hard-coded MAC address to identify the board, so we re-write our `onServiceConnected` method, removing the `retrieveBoard` method and any calls to it:

{% highlight java %}
@Override
public void onServiceConnected(ComponentName name, IBinder service) {
    ///< Typecast the binder to the service's LocalBinder class
    mwBoard= ((MetaWearBleService.LocalBinder) service).getMetaWearBoard(btDevice);
    mwBoard.setConnectionStateHandler(stateHandler);
}
{% endhighlight %}

Because this is taking place in a separate activity, we now need to move all our `getModule` calls, and all our switch code into the `onServiceConnected`, removing it from where it was previously in the `stateHandler` `connected()` block. If you find your app not working, this is probably the reason.


As a result of these changes we no longer need a manual "connect" button, so instead let's replace it with a "disconnect" button.

First, we update the UI. In *activity_my.xml* update the connect button as follows:

{% highlight java %}
<Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Disconnect"
        android:id="@+id/disconnect"
        android:layout_alignParentTop="true"
        android:layout_centerHorizontal="true" />
{% endhighlight %}

Now we update the `onClick` listener in our `onCreate` method:

{% highlight java %}
connect=(Button)findViewById(R.id.disconnect);
connect.setOnClickListener(new View.OnClickListener() {
    @Override
    public void onClick(View v) {
        Log.i(TAG, "Clicked disconnect");
        mwBoard.setConnectionStateHandler(null);
        mwBoard.disconnect();
        finish();
    }
});
{% endhighlight %}


That's it! Now when we run our app we start out on our *ScannerActivity* screen, where we can select the board. Upon selecting the board, we are connected and our LED, accelerometer and gyroscope interaction can begin. At any time we can disconnect from the board, at which point we are returned to the scan screen. 

As with all the other posts in the series, the code is availabe on github. Check branch **version-0.9** in the [github repo](https://github.com/ChristopherGS/MetaWearGuide/tree/version-0.9){:target="_blank"}

###Conclusion

If you've followed this series all the way through, you should now be ready to use other sensors on your board, and try different combinations of board features. The MetaWear platform is very versatile, and there are many areas I was not able to touch on in this introduction. Hopefully this guide helped to get you over the initial learning curve, so you can focus on building something exciting.

Some suggested resources to help you continue:

- The [Android API docs](https://mbientlab.com/androiddocs/)
- The [Android Sample app](https://github.com/mbientlab/Metawear-SampleAndroidApp)
- The [Metawear Community Forum](http://community.mbientlab.com/)

Finally, a big thank you to the team at mbientlab - you are guys are doing a great job. Keep it up!