---
title: "Initialize SDK"
slug: "initialize-sdk-android"
hidden: false
createdAt: "2019-08-21T20:54:39.140Z"
updatedAt: "2019-08-21T20:55:03.551Z"
---
Use the `instantiate` method to initialize the Android SDK and instantiate an instance of the Optimizely client class that exposes API methods like [Get Enabled Features](doc:get-enabled-features-android). Each client corresponds to the datafile representing the state of a project for a certain environment.

In the Android SDK, you don't need to manage the datafile directly. The SDK has an additional abstraction called a **manager** that handles getting the datafile and instantiating the client for you during the SDK's initialization process. The manager includes support for [datafile polling](#section-configure-datafile-polling) to automatically update the datafile on a regular interval.
[block:api-header]
{
  "title": "Version"
}
[/block]
3.1.1
[block:api-header]
{
  "title": "Description"
}
[/block]
The constructor accepts a configuration object to configure Optimizely.

Some parameters are optional because the SDK provides a default implementation, but you may want to override these for your production environments. For example, you may want override these to set up an [error handler](doc:customize-error-handler-android) and [logger](doc:customize-logger-android) to catch issues, an event dispatcher to manage network calls, and a User Profile Service to ensure sticky bucketing.
[block:api-header]
{
  "title": "Parameters"
}
[/block]
The table below lists the required and optional parameters in Android (client constructed using Builder class and passing the following to `Build()`).
[block:parameters]
{
  "data": {
    "h-0": "Parameter",
    "h-1": "Type",
    "h-2": "Description",
    "0-0": "**datafile**\n*optional* ",
    "0-1": "string",
    "0-2": "The JSON string representing the project.",
    "1-0": "**errorhandler**\n*optional*",
    "1-1": "IErrorHandler",
    "1-2": "An error handler object to handle errors."
  },
  "cols": 3,
  "rows": 2
}
[/block]

[block:api-header]
{
  "title": "Examples"
}
[/block]
The manager is implemented as a factory for Optimizely client instances. To use it, create a manager object by supplying your **SDK key** and optional configuration settings for [datafile polling](#section-configure-datafile-polling) and [datafile bundling](#section-enable-bundled-datafiles). You can find the SDK key in the *Settings > Environments* tab.

Next, choose whether to instantiate the client [synchronously or asynchronously](#section-use-synchronous-or-asynchronous-initialization) and use the appropriate `initialize` method to instantiate a client.
[block:code]
{
  "codes": [
    {
      "code": "// In your Application#onCreate\n\nimport com.optimizely.ab.android.sdk.OptimizelyManager;\nimport com.optimizely.ab.android.sdk.OptimizelyClient;\nimport com.optimizely.ab.android.sdk.OptimizelyStartListener;\nimport com.optimizely.ab.config.Variation;\n\n// Build a manager\nOptimizelyManager optimizelyManager = OptimizelyManager.builder()\n    .withSDKKey(\"SDK_KEY_HERE\")\n    .withEventDispatchInterval(60L * 10L)\n    .withDatafileDownloadInterval(60L * 10L)\n    .build(getApplicationContext());\n\n// Instantiate a client synchronously with a bundled datafile\nString datafile = \"REPLACE_WITH_YOUR_DATAFILE\";\nOptimizelyClient optimizelyClient = optimizelyManager.initialize(this, datafile);\n\n// Or, instantiate it asynchronously with a callback\noptimizelyManager.initialize(this, null, new OptimizelyStartListener() {\n    @Override\n    public void onStart(OptimizelyClient optimizelyClient) {\n        // Activate experiments\n        Variation variation = optimizelyClient.activate(\"experimentKey\", userID);\n    }\n});\n\n",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]
See the [Android example: `_MainActivity.java`](https://gist.github.com/mauerbac/64d2c638b72e38de603f511601dd2b91).
[block:api-header]
{
  "title": "Get a client"
}
[/block]
Each manager retains a reference to its most recently initialized client. You can use the `getOptimizely` function to return that instance.

If this method is called before any client objects have been initialized, a dummy client instance is created and returned. This dummy instance logs errors when any of its methods are used.
[block:code]
{
  "codes": [
    {
      "code": "OptimizelyClient optimizelyClient = optimizelyManager.getOptimizely();\n\n",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Use synchronous or asynchronous initialization"
}
[/block]
You have two choices for when to instantiate the Optimizely client: **synchronous** and **asynchronous**. The *behavioral* difference between the two methods is whether your application pings the Optimizely servers for a new datafile during initialization. The *functional* difference between the two methods is whether your app prioritizes accuracy or speed.

### Synchronous initialization

The synchronous method prioritizes speed over accuracy. Instead of attempting to download a new datafile every time you initialize an Optimizely client, your app uses a local version of the datafile. This local datafile can be cached from a previous network request (see more about [configuring datafile polling](#section-configure-datafile-polling)) or embedded within your app (see more about [enabling bundled datafiles](#section-enable-bundled-datafiles)).

When you initialize a client synchronously, the Optimizely manager first searches for a [cached datafile](#section-configure-datafile-polling). If one is available, the manager uses it to complete the client initialization. If the manager can't find a cached datafile, the manager searches for a [bundled datafile](#section-enable-bundled-datafiles). If the manager finds a the bundled datafile, it uses the datafile to complete the client initialization. If the manager can't find a bundled datafile, the manager can't initialize the client.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/1085e10-initialize-synchronous.png",
        "initialize-synchronous.png",
        1200,
        760,
        "#ccd8e1"
      ],
      "sizing": "80",
      "border": true
    }
  ]
}
[/block]
To initialize an `OptimizelyClient` object **synchronously**, call `OptimizelyManager#initialize()` and provide two arguments:

* The Application instance (from `android.app.Application`)
* An `Integer` pointer to the application resource datafile

For more details on the specific requirements, view [`OptimizelyManager.java` from the publicly available Android SDK](https://github.com/optimizely/android-sdk/blob/master/android-sdk/src/main/java/com/optimizely/ab/android/sdk/OptimizelyManager.java).

### Asynchronous initialization

The asynchronous method prioritizes accuracy over speed. During initialization, your app requests the newest datafile from the CDN servers. Requesting the newest datafile ensures that your app always uses the most current project settings, but it also means your app cannot instantiate a client until it downloads a new datafile, discovers the datafile has not changed, or until the request times out. This process takes time.

Initializing a client asynchronously executes like the [synchronous initialization](#section-synchronous-initialization), except the manager will first attempt to download the newest datafile. This network activity is what causes an asynchronous initialization to take longer to complete.

If the network request returns an error (such as when network connectivity is unreliable) or if the manager discovers that the cached datafile is identical to the newest datafile, the manager then uses the synchronous approach. If manager discovers that the datafile has been updated and now differs from the cached datafile, the manager downloads the new datafile and uses it to initialize the client.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/ee8b24d-initialize-asynchronous.png",
        "initialize-asynchronous.png",
        1013,
        1200,
        "#c8d7de"
      ],
      "sizing": "80",
      "border": true
    }
  ]
}
[/block]
To initialize an `OptimizelyClient` object **asynchronously**, call `OptimizelyManager#initialize()` and three arguments:

* The Application instance (from `android.app.Application`)
* An `Integer` pointer to the application resource datafile
* A listener object

For more details on the specific requirements and to see how to build each object, view [`OptimizelyManager.java` from the publicly available Android SDK](https://github.com/optimizely/android-sdk/blob/master/android-sdk/src/main/java/com/optimizely/ab/android/sdk/OptimizelyManager.java).
[block:api-header]
{
  "title": "Configure datafile polling"
}
[/block]
During its initialization, the manager attempts to pull the newest datafile from the CDN servers. After this, the Optimizely manager can periodically check for and pull a new datafile at the time interval you set during its initialization. The process of checking for and downloading a new datafile is called *datafile polling*.

By default, the manager only checks for a new datafile during initialization. To enable periodic datafile polling, you **must** define a polling interval when building the manager. This value is the number of seconds the manager waits between datafile polling attempts. 
[block:code]
{
  "codes": [
    {
      "code": "// Poll every 2 minutes\nOptimizelyManager optimizelyManager = OptimizelyManager.builder()\n  .withSDKKey(\"SDK_KEY_HERE\")\n  .withDatafileDownloadInterval(60L * 2L)\n  .build(getApplicationContext());\n\n",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]
### Usage notes

* The minimum polling interval is 60 seconds.
* Updated datafiles do not take effect until your app is restarted or when you re-initialize the Optimizely manager. This implementation strategy allows the data to change while the app is running without causing nondeterministic behavior.
* The Optimizely manager only checks for new datafiles when the SDK is active and the app is running. Datafile polling is never done while the app is in the background.

To override the default datafile handler and use your own, implement [DatafileHandler](https://github.com/optimizely/android-sdk/blob/master/datafile-handler/src/main/java/com/optimizely/ab/android/datafile_handler/DatafileHandler.java).
[block:api-header]
{
  "title": "Enable bundled datafiles"
}
[/block]
When your customer opens your app for the first time after installing or updating it, the manager attempts to pull the newest datafile from Optimizely's CDN. If your app is unable to contact the servers, the manager can substitute a datafile that you included (_bundled_) when you created your app.

By bundling a datafile within your app, you ensure that the Optimizely manager can initialize without waiting for a response from the CDN. This lets you prevent poor network connectivity from causing your app to hang or crash while it loads.

Datafile bundling works with both synchronous and asynchronous initialization because reverting to a bundled datafile happens during the Optimizely manager's initialization, before a client is instantiated.
[block:code]
{
  "codes": [
    {
      "code": "/**\n * Initialize Optimizely asynchronously with a datafile.\n *  If it is not able to download a new datafile, it will \n *  initialize an OptimizelyClient with the one provided.\n */\noptimizelyManager.initialize(this, R.raw.datafile, new OptimizelyStartListener() {\n    @Override\n    public void onStart(OptimizelyClient optimizely) {\n        startVariation();\n    }\n});\n\n/** \n* Initialize Optimizely synchronously\n*  This will immediately instantiate and return an \n*  OptimizelyClient with the datafile that was passed in. \n*  It'll also download a new datafile from the CDN and \n*  persist it to local storage.\n*  The newly downloaded datafile will be used the next \n*  time the SDK is initialized.\n*/\noptimizelyManager.initialize(myApplication, R.raw.datafile);\n\n",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Source files"
}
[/block]
The language/platform source files containing the implementation for Android are at [OptimizelyClient.java](https://github.com/optimizely/android-sdk/tree/master/android-sdk/src/main/java/com/optimizely/ab/android/sdk).