---
title: "Get Enabled Features"
slug: "get-enabled-features-android"
hidden: false
createdAt: "2019-08-21T21:01:38.133Z"
updatedAt: "2019-09-13T11:54:11.176Z"
---
Retrieves a list of features that are enabled for the user. Invoking this method is equivalent to running [Is Feature Enabled](doc:is-feature-enabled-android) for each feature in the datafile sequentially.

This method takes into account the user `attributes` passed in, to determine if the user is part of the audience that qualifies for the experiment.  
[block:api-header]
{
  "title": "Version"
}
[/block]
SDK v3.0
[block:api-header]
{
  "title": "Description"
}
[/block]
This method iterates through all feature flags and for each feature flag invokes [Is Feature Enabled](doc:is-feature-enabled-android). If a feature is enabled, this method adds the feature’s key to the return list.
[block:api-header]
{
  "title": "Parameters"
}
[/block]
The table below lists the required and optional parameters in Android.
[block:parameters]
{
  "data": {
    "h-0": "Parameter",
    "h-1": "Type",
    "h-2": "Description",
    "0-0": "**userId**\n*required*",
    "0-1": "string",
    "1-0": "**Attributes**\n*optional*",
    "1-1": "map",
    "0-2": "The ID of the user to check. For more information, see [Handle user IDs](doc:handle-user-ids).",
    "1-2": "A map of custom key-value string pairs specifying attributes for the user that are used for [audience targeting](doc:audiences). Non-string values are only supported in the 3.0 SDK and above."
  },
  "cols": 3,
  "rows": 2
}
[/block]

[block:api-header]
{
  "title": "Returns"
}
[/block]
A list of keys corresponding to the features that are enabled for the user, or an empty list if no features could be found for the specified user.
[block:api-header]
{
  "title": "Examples"
}
[/block]
This section shows a simple example of how you can use the method.
[block:code]
{
  "codes": [
    {
      "code": "List<String> enabledFeatures = optimizelyClient.getEnabledFeatures(GENERIC_USER_ID,\n       Collections.singletonMap(\"house\", \"Gryffindor\"));\n\n",
      "language": "java"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Source files"
}
[/block]
The language/platform source files containing the implementation for Android is [OptimizelyClient.java](https://github.com/optimizely/android-sdk/tree/master/android-sdk/src/main/java/com/optimizely/ab/android/sdk).