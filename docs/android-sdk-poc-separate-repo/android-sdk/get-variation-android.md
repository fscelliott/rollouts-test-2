---
title: "Get Variation"
slug: "get-variation-android"
hidden: false
createdAt: "2019-09-11T13:49:10.118Z"
updatedAt: "2019-09-12T20:16:12.636Z"
---
Returns a variation where the visitor will be bucketed, without triggering an impression.
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
Takes the same arguments and returns the same values as [Activate](doc:activate-android), but without sending an impression network request. The behavior of the two methods is identical otherwise. 

Use Get Variation if Activate has been called and the current variation assignment is needed for a given experiment and user. This method bypasses redundant network requests to Optimizely.

See [Implement impressions](doc:implement-impressions) for guidance on when to use each method.
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
    "0-2": "The key of the experiment.",
    "1-2": "The ID of the user.",
    "2-2": "A map of custom key-value string pairs specifying attributes for the user that are used for audience targeting and results segmentation. Non-string values are only supported in the 3.0 SDK and above.",
    "2-1": "map",
    "2-0": "**attributes**\n*optional*"
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
 The the variation for the provided experiment key, user id, and attributes.
[block:api-header]
{
  "title": "Example"
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "import com.optimizely.ab.config.Variation;\n\nMap<String, Object> attributes = new HashMap<>();\nattributes.put(\"device\", \"iPhone\");\nattributes.put(\"lifetime\", 24738388);\nattributes.put(\"is_logged_in\", true);\n\nVariation variation = optimizelyClient.getVariation(\"my_experiment_key\", \"user_123\", attributes);\n\n",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Notes"
}
[/block]
### Activate versus Get Variation

Use Activate when the visitor actually sees the experiment. Use Get Variation when you need to know which bucket a visitor is in before showing the visitor the experiment. Impressions are tracked by [Is Feature Enabled](doc:is-feature-enabled-android) when there is a feature test running on the feature and the visitor qualifies for that feature test.

For example, suppose you want your web server to show a visitor variation_1 but don't want the visitor to count until they open a feature that isn't visible when the variation loads, like a modal. In this case, use Get Variation in the backend to specify that your web server should respond with variation_1, and use Activate in the front end when the visitor sees the experiment.

Also, use Get Variation when you're trying to align your Optimizely results with client-side third-party analytics. In this case, use Get Variation to retrieve the variation, and even show it to the visitor, but only call Activate when the analytics call goes out.

See [Implement impressions](doc:implement-impressions) for more information about whether to use Activate or Get Variation for a call.
[block:callout]
{
  "type": "warning",
  "title": "Important",
  "body": "Conversion events can only be attributed to experiments with previously tracked impressions. Impressions are tracked by Activate, not by Get Variation. As a general rule, Optimizely impressions are required for experiment results and not only for billing."
}
[/block]

[block:api-header]
{
  "title": "Source files"
}
[/block]
The language/platform source files containing the implementation for Android is [OptimizelyClient.java](https://github.com/optimizely/android-sdk/tree/master/android-sdk/src/main/java/com/optimizely/ab/android/sdk).