---
layout: post
title: MetaWear Guide Series Part 8
category : IOT
tagline: Writing to CSV
tags : [IOT, Metawear, tutorial]
---

*This is part 8 of a multipart series showing you how to get started with the MetaWear platform. [View the contents of the series to easily skip forwards or backwards]({{ site.baseurl }}/iot/2016/01/17/metawear-guide-pt1/)*

For anyone looking to do analysis of the data they are collecting with their board, it is likely that you will want to access the data so you can analyze it using languages/tools like Python, R or Matlab. The easiest way to get started here is by writing your sensor readings to a comma separated values (CSV) file. Let's see how to do it.

We will need to import a few libraries to assist:

{% highlight java %}
import java.io.BufferedOutputStream;
import java.io.File;
import java.io.FileOutputStream;
import java.io.OutputStream;
{% endhighlight %}

Now, in our `onCheckChanged` switch function, we instantiate more variables for our CSV writing:

{% highlight java %}
public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
    Log.i("Switch State=", "" + isChecked);
    final String filename = "METAWEAR.csv";
    final File path = new File(Environment.getExternalStoragePublicDirectory(
            Environment.DIRECTORY_DOWNLOADS), filename);
{% endhighlight %}

The File we are creating in the above code will be located in your downloads directory. [See more information about Android Environment variabes](http://developer.android.com/reference/android/os/Environment.html).

Within our route subscription, we add the main CSV writing code:

{% highlight java %}
@Override
public void process(Message msg) {
    final CartesianFloat axes = msg.getData(CartesianFloat.class);
    Log.i(TAG, String.format("Accelerometer: %s", axes.toString()));
    sensorMsg(String.format(axes.toString()), "accel");
    //CSV CODE
    String accel_entry = String.format("Accel, %s", axes.toString());
    String csv_accel_entry = accel_entry + ",";
    OutputStream out;
    try {
        out = new BufferedOutputStream(new FileOutputStream(path, true));
        out.write(csv_accel_entry.getBytes());
        out.write("\n".getBytes());
        out.close();
    } catch (Exception e) {
        Log.e(TAG, "CSV creation error", e);
    }
}
{% endhighlight %}

A few points to note from the above code:

1. We append a comma to the end of our `csv_accel_entry` variable, to separate the readings (i.e. columns). 
2. Our CSV writing code must be wrapped in a `try catch` block
3. We add a line break (`\n`) to separate the entries


At this point, the bare bones of our CSV writing code are complete, but if you were to run the app you would receive an `EACCES (Permission denied)` error. To remedy this, we must grant certain permissions within our *AndroidManifest.xml* file. Add the following line before your `<application` tag:

`<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />`

Now run the app. After you toggle the switch on and off, you should be able to find a file called `METAWEAR.csv` in your downloads folder. You may find it easier to download and use one of the many free Android OS file manager apps [such as 'file explorer'](https://play.google.com/store/apps/details?id=nextapp.fx&hl=zh_TW) for this purpose.

Your CSV file should look like this:

![pt8-csv]({{site.baseurl}}/assets/images/metawear/metawear_ss_16.png)

<br>

So far so good. Now we duplicate the code in the Gyroscope route (see the [github version-0.7 branch](https://github.com/ChristopherGS/MetaWearGuide/tree/version-0.7){:target="_blank"} if you have any trouble). Now when you open your csv file you will see alternating rows of accelerometer and gyroscope data. 

To make sure your two sensor readings in sync, you must ensure that the [Output Data Rate (ODR)](https://mbientlab.com/docs/metawear/android/latest/com/mbientlab/metawear/module/Accelerometer.html#setOutputDataRate(float)) for both sensors is configured to the same frequency. Knowing this, you can store the data you received in order, and use the sensor's  and the data's list index to compute approximately when said data was received relative to the starting time.

It's likely that we will want a header in our CSV file. We add this earlier in our code, writing the header before other data:

{% highlight java %}
public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
	Log.i("Switch State=", "" + isChecked);
	final String CSV_HEADER = String.format("sensor,x_axis,y_axis,z_axis"); //add the header
	final String filename = "METAWEAR.csv";
	final File path = new File(Environment.getExternalStoragePublicDirectory(
	        Environment.DIRECTORY_DOWNLOADS), filename);
	if (isChecked) {
	    //prep();
	    OutputStream out;
	    try {
	        out = new BufferedOutputStream(new FileOutputStream(path, true));
	        out.write(CSV_HEADER.getBytes());
	        out.write("\n".getBytes());
	        out.close();
	    } catch (Exception e) {
	        e.printStackTrace();
	    }
{% endhighlight %}

Finally, in its current form, every new entry will be appended to the existing CSV file. This may not be desirable, so we add a couple of lines of code which delete the previous CSV file before creating a new one everytime the switch is toggled. If this behaviour is not what you require, you can omit this, or put it into a separate method if you wish to add more complex logic:

{% highlight java %}
boolean is_deleted = path.delete();
Log.i(TAG, "deleted previous csv: " + is_deleted);
{% endhighlight %}
                            
You now have a CSV file which you can use for your data analysis. In the next section we will discuss ensuring the connection to your board is reliable.

*Note* You can view all these changes in the [github repository](https://github.com/ChristopherGS/MetaWearGuide/tree/version-0.7){:target="_blank"} on the branch **version-0.7**

[Next post in the series -->]({{ site.baseurl }}/iot/2016/02/28/metawear-guide-pt9/)



