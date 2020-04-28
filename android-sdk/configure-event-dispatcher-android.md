---
title: "Configure event dispatcher"
slug: "configure-event-dispatcher-android"
hidden: false
createdAt: "2019-09-11T13:38:34.480Z"
updatedAt: "2019-12-04T17:41:10.426Z"
---
The Optimizely SDKs make HTTP requests for every impression or conversion that gets triggered. Each SDK has a built-in **event dispatcher** for handling these events, but we recommend overriding it based on the specifics of your environment.

The Android SDK has an out-of-the-box asynchronous dispatcher. We recommend customizing the event dispatcher you use in production to ensure that you queue and send events in a manner that scales to the volumes handled by your application. Customizing the event dispatcher allows you to take advantage of features like batching, which makes it easier to handle large event volumes efficiently or to implement retry logic when a request fails. You can build your dispatcher from scratch or start with the provided dispatcher.

The examples show that to customize the event dispatcher, initialize the Optimizely client (or manager) with an event dispatcher instance.
[block:code]
{
  "codes": [
    {
      "code": "import com.optimizely.ab.android.sdk.OptimizelyManager;\nimport com.optimizely.ab.event.EventHandler;\nimport com.optimizely.ab.android.event_handler.EventRescheduler;\n\n// Using an anonymous class here to implement the EventHandler interface. \n// Feel free to create an explicit class that implements the interface instead.\nEventHandler eventHandler = new EventHandler() {\n    @Override\n    public void dispatchEvent(LogEvent logEvent) throws Exception {\n        // Send event to our log endpoint as documented in https://developers.optimizely.com/x/events/api/index.html\n    }\n};\n\n// Build a manager\noptimizelyManager = OptimizelyManager.builder()\n  .withSDKKey(\"SDK_KEY_HERE\")\n  .withEventDispatchInterval(60L * 10L)\n  .withEventHandler(eventHandler)\n  .withDatafileDownloadInterval(60L * 10L)\n  .build(getApplicationContext());\n\n/** \n * With the new Android O differences, you need to register the\n * service for the intent filter you desire in code instead of \n * in the manifest.\n */\nEventRescheduler eventRescheduler = new EventRescheduler();\n\ngetApplicationContext().registerReceiver(eventRescheduler, new IntentFilter(WifiManager.SUPPLICANT_CONNECTION_CHANGE_ACTION));\n\n",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]
The event dispatcher should implement a `dispatchEvent` function, which takes in three arguments: `httpVerb`, `url`, and `params`, all of which are created by the internal `EventBuilder` class. In this function, you should send a `POST` request to the given `url` using the `params` as the body of the request (be sure to stringify it to JSON) and `{content-type: 'application/json'}` in the headers.
[block:callout]
{
  "type": "warning",
  "title": "Important",
  "body": "If you are using a custom event dispatcher, do not modify the event payload returned from Optimizely. Modifying this payload will alter your results."
}
[/block]
The included event handler implementation includes queueing and flushing, so it will work even if an app is offline. The event handler uses an `IntentService` to queue up events to dispatch. If they fail to send, they are stored in a `sqlite` database and scheduled to be dispatched later. If you want to have events flushed when Wi-Fi is available or after reboot or reinstall using the default event handler, you need to augment your AndroidManifest.xml to include the intent filter declarations.
[block:code]
{
  "codes": [
    {
      "code": "//  Add these lines to your manifest if you want the event handler background services to schedule themselves again after a boot or package replace.\n<receiver\n    android:name=\"com.optimizely.ab.android.event_handler.EventRescheduler\"\n    android:enabled=\"true\"\n    android:exported=\"false\">\n    <intent-filter>\n        <action android:name=\"android.intent.action.MY_PACKAGE_REPLACED\" />\n        <action android:name=\"android.intent.action.BOOT_COMPLETED\" />\n        <action android:name=\"android.net.wifi.supplicant.CONNECTION_CHANGE\" />\n    </intent-filter>\n</receiver>\n            \n            ",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]
You will need to implement [EventHandler](https://github.com/optimizely/java-sdk/blob/master/core-api/src/main/java/com/optimizely/ab/event/EventHandler.java) to use your own event handler.
[block:callout]
{
  "type": "info",
  "title": "Note",
  "body": "To handle backgrounding in Android O and later, you also need to register your event handler rescheduler in code. See the code sample above."
}
[/block]