---
title: "Android Oreo Notification Channels"
date: 2018-01-31T18:02:31+01:00
draft: true
---
Google introduced the new notification channels in their latest Android version: Android Oreo (8.0). The notification channel might seem a bit similat to the notification groups, that have been added in Android Nougat, but this is something completely different!

<p style="text-align: center;">
	<img src="/static/images/android-oreo-notification-channel.png" alt="Demo image" style="max-height: 500px;" />
</p>

The notification channels allow the user to adjust the settings for each of those notifcation channels. Let's say we have a chat app, that has three channels:
1. New message notification
2. Friend request
3. App news
The user wants to always receive new messages and so he would choose, that this notification will play a sound and vibrate the phone. Also could he decide, whether the notification should peek into the screen.
Normally news or updates about the app are not that important for the users. With the new notification channels the user could choose, to not play a sound or vibrate as he likes, because it would be enough for him to see the notification, if he unlocks his phone.

To implement the new notification channels just follow those steps:

## Step 1
To start you have to create the channels, so that they will appear within the android settings:
~~~java
//at first check, if the notification channels are available (starting at Android Oreo)
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {

    // create a new notification channel
    NotificationChannel testChannel = new NotificationChannel("Channel id",
        "Name that will be visible in the settings",
        NotificationManager.IMPORTANCE_DEFAULT);

    testChannel.setDescription("You can add a description here");

    //enable or disable the notification light and set it's color
    testChannel.enableLights(true);
    testChannel.setLightColor(Color.BLUE);

    //enable or disable the vibration
    testChannel.enableVibration(true);

    //show a badge in supported launchers
    testChannel.setShowBadge(true);

    //add this channel to the system
    getManager().createNotificationChannel(testChannel);

    //of course you can create as many channels as you'd like
}
~~~
There are also more options, just check the [android reference: NotificationChannel](https://developer.android.com/reference/android/app/NotificationChannel.html)

## Step 2
After you created all channels that you need you can easily integrate them with your notification creation code.
Just add the channel id to the builder and it's done.
~~~java
//create a NotificationCompat Builder and pass after the context the channel you need
//it has to be one, that you already added in step 1
NotificationCompat.Builder notification = new NotificationCompat.Builder(getContext(), "Channel id");

//setup your notification as always
notification.setContentTitle("Test Notification");
...

//just display the notification
NotificationManager mNotificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);

mNotificationManager.notify(id, notification.build());
~~~
You should use the <code>NotificationCompat.Builder</code>, because it ensures the backwards compatibility, so it will also work on android version below 8.0

Practically that is everything you have to do, to make your notifications work under the newest android version.
