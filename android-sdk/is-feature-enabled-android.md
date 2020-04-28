---
title: "Is Feature Enabled"
slug: "is-feature-enabled-android"
hidden: false
createdAt: "2019-08-21T21:02:12.215Z"
updatedAt: "2019-08-22T21:08:42.233Z"
---
Determines whether a feature is enabled for a given user. The purpose of this method is to allow you to separate the process of developing and deploying features from the decision to turn on a feature. Build your feature and deploy it to your application behind this flag, then turn the feature on or off for specific users.
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
This method traverses the client's [datafile](doc:get-the-datafile) and checks the feature flag for the feature key that you specify.
1. Analyzes the user's attributes.
2. Hashes the [userID](doc:handle-user-ids).

The method then evaluates the [feature rollout](doc:create-feature-flags) for a user. The method checks whether the rollout is enabled, whether the user qualifies for the [audience targeting](doc:target-audiences), and then randomly assigns either `on` or `off` based on the appropriate traffic allocation. If the feature rollout is on for a qualified user, the method returns `true`. 
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
    "0-0": "**featureKey**\n_required_",
    "0-1": "string",
    "1-0": "**userId**\n_required_",
    "1-1": "string",
    "2-0": "**attributes**\n_optional_",
    "2-1": "map",
    "h-2": "Description",
    "0-2": "The key of the feature to check. \n\nThe feature key is defined from the Features dashboard. For more information, see [Create feature flags](doc:create-feature-flags).",
    "1-2": "The ID of the user to check. For more information, see [Handle user IDs](doc:handle-user-ids).",
    "2-2": "A map of custom key-value string pairs specifying attributes for the user that are used for [audience targeting](doc:target-audiences). Non-string values are only supported in the 3.0 SDK and above."
  },
  "cols": 3,
  "rows": 3
}
[/block]

[block:api-header]
{
  "title": "Returns"
}
[/block]
True if the feature is enabled. False if the feature is disabled or not found.
[block:api-header]
{
  "title": "Examples"
}
[/block]
None
[block:api-header]
{
  "title": "Exceptions"
}
[/block]
None
[block:api-header]
{
  "title": "Source files"
}
[/block]
The language/platform source files containing the implementation for Android is [OptimizelyClient.java](https://github.com/optimizely/android-sdk/tree/master/android-sdk/src/main/java/com/optimizely/ab/android/sdk).