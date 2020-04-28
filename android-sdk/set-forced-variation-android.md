---
title: "Set Forced Variation"
slug: "set-forced-variation-android"
hidden: false
createdAt: "2019-09-11T13:50:19.989Z"
updatedAt: "2019-09-12T20:18:35.246Z"
---
Forces a user into a variation for a given experiment for the lifetime of the Optimizely client.

The purpose of this method is to force a user into a specific variation or personalized experience for a given experiment. The forced variation value doesn't persist across application launches.
[block:api-header]
{
  "title": "Version"
}
[/block]
SDK v3.0, v3.1
[block:api-header]
{
  "title": "Description"
}
[/block]
Forces a user into a variation for a given experiment for the lifetime of the Optimizely client. Any future calls to [Activate](doc:activate-android), [Is Feature Enabled](doc:is-feature-enabled-android), [Get Feature Variable](doc:get-feature-variable-android), and [Track](doc:track-android) for the given user ID returns the forced variation.

Forced bucketing variations take precedence over whitelisted variations, variations saved in a User Profile Service (if one exists), and the normal bucketed variation. Impression and conversion events are still tracked when forced bucketing is enabled.

Variations are overwritten with each set method call. To clear the forced variations so that the normal bucketing flow can occur, pass null as the variation key parameter. To get the variation that has been forced, use [Get Forced Variation](doc:get-forced-variation-android).

This call will fail and return false if the experiment key is not in the project file or if the variation key is not in the experiment.

You can also use Set Forced Variation for [feature tests](doc:run-feature-tests).
[block:api-header]
{
  "title": "Parameters"
}
[/block]
The required and optional parameters for Android are listed below.
[block:parameters]
{
  "data": {
    "h-0": "Parameter",
    "h-1": "Type",
    "h-2": "Description",
    "0-0": "**experimentKey**\n*required*",
    "0-1": "string",
    "1-0": "**userId**\n*required*",
    "1-1": "string",
    "0-2": "The key of the experiment to set with the forced variation.",
    "1-2": "The ID of the user to force into the variation.",
    "2-2": "The key of the forced variation. Set the value to `null` to clear the existing experiment-to-variation mapping.",
    "2-0": "**variationKey**\n*optional*",
    "2-1": "string"
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
Boolean: `true` if the user was successfully forced into a variation, `false` if the `experimentKey` isn't in the project file or the `variationKey` isn't in the experiment.
[block:api-header]
{
  "title": "Example"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "optimizelyClient.setForcedVariation(\"my_experiment_key\", \"user_123\", \"some_variation_key\")\n  \n  ",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Side effects"
}
[/block]
In the receiving client instance, sets the forced variation for the specified user in the specified experiment. This forced variation is used instead of the variation that Optimizely would normally determine for that user and experiment.
[block:api-header]
{
  "title": "Source files"
}
[/block]
The language/platform source files containing the implementation for Android is [OptimizelyClient.java](https://github.com/optimizely/android-sdk/tree/master/android-sdk/src/main/java/com/optimizely/ab/android/sdk).