---
title: "Event batching"
slug: "event-batching-android"
hidden: false
createdAt: "2019-12-12T22:57:36.892Z"
updatedAt: "2020-01-31T18:21:39.223Z"
---
The [Optimizely Full Stack Android SDK](https://github.com/optimizely/android-sdk) now batches impression and conversion events into a single payload before sending it to Optimizely. This is achieved through a new SDK component called the event processor.

Event batching has the advantage of reducing the number of outbound requests to Optimizely depending on how you define, configure, and use the event processor. It means less network traffic for the same number of Impression and conversion events tracked.

In the Android SDK, `BatchEventProcessor` provides implementation of the `EventProcessor` interface and batches events. You can control batching based on two parameters:

- Batch size: Defines the number of events that are batched together before sending to Optimizely.
- Flush interval: Defines the amount of time after which any batched events should be sent to Optimizely.

An event consisting of the batched payload is sent as soon as the batch size reaches the specified limit or flush interval reaches the specified time limit. `BatchEventProcessor` options are described in more detail below.
[block:callout]
{
  "type": "info",
  "title": "Note",
  "body": "Event batching works with both out-of-the-box and custom event dispatchers.\n\nThe event batching process doesn't remove any personally identifiable information (PII) from events. You must still ensure that you aren't sending any unnecessary PII to Optimizely."
}
[/block]

[block:api-header]
{
  "type": "basic",
  "title": "Basic example"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "import com.optimizely.ab.android.sdk.OptimizelyManager;\nimport android.support.v7.app.AppCompatActivity;\n\npublic class MainActivity extends AppCompatActivity {\n\n    @Override\n    protected void onCreate(Bundle savedInstanceState) {\n        String sdkKey = \"{SDK_KEY}\";\n      \n      \tOptimizelyManager optimizelyManager = OptimizelyManager.builder()\n          .withSDKKey(sdkKey)\n          .build(getApplicationContext());\n        Optimizely optimizely = optimizelyManager.initialize(MainActivity.this, R.raw.datafile);\n    }\n}\n",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]
By default, batch size is 10 and flush interval is 30 seconds.
[block:api-header]
{
  "title": "Advanced Example"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "import com.optimizely.ab.android.sdk.OptimizelyManager;\nimport com.optimizely.ab.android.sdk.OptimizelyClient;\nimport com.optimizely.ab.event.BatchEventProcessor;\nimport java.util.concurrent.TimeUnit;\n\npublic class MainActivity extends AppCompatActivity {\n\n    @Override\n    protected void onCreate(Bundle savedInstanceState) {\n      String sdkKey = \"{SDK_KEY}\";\n      EventHandler eventHandler = DefaultEventHandler.getInstance(context);\n\n      // Here we are using the builder options to set batch size\n      // to 50 events and flush interval to a minute.\n      BatchEventProcessor batchProcessor = BatchEventProcessor.builder()\n        .withBatchSize(50)\n        .withEventHandler(eventHandler)\n        .withFlushInterval(1, TimeUnit.MINUTES)\n        .build();\n      \t\n      OptimizelyManager optimizelyManager = OptimizelyManager.builder()\n        .withSDKKey(sdkKey)\n        .withEventHandler(eventHandler)\n        .withDatafileDownloadInterval(60L * 10L)\n        .withEventProcessor(batchProcessor)\n        .build(getApplicationContext());\n      \n      OptimizelyClient optimizely = optimizelyManager.initialize(getApplicationContext(), R.raw.datafile);\n    }\n}",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "BatchEventProcessor"
}
[/block]
`BatchEventProcessor` is an implementation of `EventProcessor` where events are batched. The class maintains a single consumer thread that pulls events off of the `BlockingCollection` and buffers them for either a configured batch size or a maximum duration before the resulting `LogEvent` is sent to the `EventDispatcher` and `NotificationCenter`.

The following properties can be used to customize the BatchEventProcessor configuration *using the Builder class*


# Builder methods
Use the following methods to customize the `BatchEventProcessor`.
[block:parameters]
{
  "data": {
    "h-0": "Property",
    "h-1": "Default value",
    "h-2": "Description",
    "0-0": "**withEventHandler**",
    "0-1": "null",
    "0-2": "Required field. Specifies event handler to use to dispatch event payload to Optimizely.\n\nBy default, if you create an Optimizely instance, it passes in `AsyncEventHandler` to `BatchEventProcessor`.",
    "1-0": "**withBatchSize**",
    "1-1": "10",
    "1-2": "The maximum number of events to batch before dispatching. Once this number is reached, all queued events are flushed and sent to Optimizely.",
    "2-0": "**withFlushInterval**",
    "2-1": "30000 (30 Seconds)",
    "2-2": "Maximum time after which to batch and send event payload to Optimizely. In milliseconds.",
    "3-1": "1000",
    "3-0": "**withEventQueue**",
    "3-2": "BlockingCollection that queues individual events to be batched and dispatched by the executor.",
    "4-0": "**withNotificationCenter**",
    "4-1": "null",
    "4-2": "Notification center instance to be used to trigger any notifications to notify when event batches are flushed.",
    "5-0": "**withExecutor**",
    "5-1": "Single Thread Executor",
    "5-2": "Executor service that takes care of triggering event batching and subsequent dispatching to Optimizely.",
    "6-0": "**withTimeout**",
    "6-1": "5000",
    "6-2": "Maximum time to wait for event processor’s close to be executed. In milliseconds."
  },
  "cols": 3,
  "rows": 7
}
[/block]
## Advanced configuration
You can set the following properties can be set to override the default configuration for `BatchEventProcessor`.
[block:parameters]
{
  "data": {
    "0-0": "**event.processor.batch.size** ",
    "1-0": "**event.processor.batch.interval** ",
    "2-0": "**event.processor.close.timeout**",
    "0-1": "10",
    "1-1": "30000",
    "2-1": "5000",
    "0-2": "Maximum size of batch of events to send to Optimizely.",
    "1-2": "Maximum time after which to batch and send event payload to Optimizely. In milliseconds.",
    "2-2": "Maximum time to wait for event processor’s close to be executed. In milliseconds."
  },
  "cols": 3,
  "rows": 3
}
[/block]
For more information, see [Initialize SDK](doc:initialize-sdk-android).
[block:api-header]
{
  "title": "Side effects"
}
[/block]
The table lists other Optimizely functionality that may be triggered by using this class.
[block:parameters]
{
  "data": {
    "h-0": "Functionality",
    "h-1": "Description",
    "0-1": "Whenever the event processor produces a batch of events, a LogEvent object will be created using the EventFactory.\nIt contains batch of conversion and impression events. \nThis object will be dispatched using the provided event dispatcher and also it will be sent to the notification subscribers",
    "0-0": "LogEvent",
    "1-1": "Flush invokes the LOGEVENT [notification listener](doc:set-up-notification-listener-android) if this listener is subscribed to.",
    "1-0": "Notification Listeners"
  },
  "cols": 2,
  "rows": 2
}
[/block]
### Registering LogEvent listener

To register a LogEvent notification listener
[block:code]
{
  "codes": [
    {
      "code": "optimizelyClient.getNotificationCenter().addNotificationHandler(LogEvent.class, logEvent -> {});\n",
      "language": "java"
    }
  ]
}
[/block]
###  LogEvent

LogEvent object gets created using [EventFactory](https://github.com/optimizely/java-sdk/blob/master/core-api/src/main/java/com/optimizely/ab/event/internal/EventFactory.java). It represents the batch of impression and conversion events we send to the Optimizely backend.
[block:parameters]
{
  "data": {
    "h-0": "Object",
    "h-1": "Type",
    "h-2": "Description",
    "0-0": "**requestMethod**\n*Required (non null)* ",
    "0-1": "RequestMethod (Enum)",
    "0-2": "The HTTP verb to use when dispatching the log event. It can be Get or post.",
    "1-0": "**endpointUrl**\n*Required (non null)* ",
    "1-1": "String",
    "1-2": "URL to dispatch log event to.",
    "2-0": "**requestParams**\n*Required (non null)* ",
    "2-1": "Map<String, String>",
    "2-2": "Parameters to be set in the log event.",
    "3-0": "**[eventBatch](https://github.com/optimizely/java-sdk/blob/13c27ffc812928ef5cf05159d37ad6bab421fc5a/core-api/src/main/java/com/optimizely/ab/event/LogEvent.java#L38)**\n*Required*",
    "3-1": "EventBatch",
    "3-2": "It contains all the information regarding every event which is batched. including list of visitors which contains UserEvent."
  },
  "cols": 3,
  "rows": 4
}
[/block]

[block:api-header]
{
  "title": "Close Optimizely on application exit"
}
[/block]
If you enable event batching, it's important that you call the Close method (`optimizelyClient.close()`) prior to exiting. This ensures that queued events are flushed as soon as possible to avoid any data loss.
[block:callout]
{
  "type": "warning",
  "title": "Important",
  "body": "Because the Optimizely client maintains a buffer of queued events, we recommend that you call `close()` on the Optimizely instance before shutting down your application or whenever dereferencing the instance."
}
[/block]

[block:parameters]
{
  "data": {
    "h-0": "Method",
    "h-1": "Description",
    "0-0": "**close()** ",
    "0-1": "Stops all executor threads and flushes the event queue. This method will also stop any scheduledExecutorService that is running for the data-file manager."
  },
  "cols": 2,
  "rows": 1
}
[/block]