---
title: "Set up notification listener"
slug: "set-up-notification-listener-android"
hidden: false
createdAt: "2019-08-21T21:00:22.388Z"
updatedAt: "2019-08-21T21:00:30.175Z"
---
When rolling out features, you'll want to know when users are accessing a feature flag and what experience they're receiving in production.

Although Optimizely Rollouts doesn't offer analytics on feature flags out of the box, you can still track analytics on feature flag usage by adding a notification listener to send events to the analytics provider of your choice.

Notification listeners trigger a callback function that you define when certain actions are triggered in the SDK. 

The most common use case is to send a stream of all feature flag decisions to an analytics provider or to an internal data warehouse to join it with other data that you have about your users.

To track feature usage:
1. Sign up for an analytics provider of your choice (for example, Segment)
2. Set up a notification listener of type 'DECISION'
3. Follow your analytics provider's documentation and send events from within the decision listener callback

Steps 1 and 3 aren't covered in this documentation. However, setting up a 'DECISION' notification listener is covered below.
[block:api-header]
{
  "title": "Set up a DECISION notification listener"
}
[/block]
The `DECISION` notification listener enables you to be notified whenever the SDK determines what decision value to return for a feature. The callback is triggered with the decision type, associated decision information, user ID, and attributes.

`DECISION` listeners are triggered when you call [Is Feature Enabled](doc:is-feature-enabled-android) or [Get Enabled Features](doc:get-enabled-features-android).

To set up a `DECISION` listener:
  1. Define a callback to be called when the `DECISION` event is triggered
  2. Add the callback to the notification center on the Optimizely instance

The example code below shows how to add a listener, remove a listener, remove all listeners of a specific type (such as all decision listeners), and remove all listeners.
[block:code]
{
  "codes": [
    {
      "code": "import com.optimizely.ab.notification.NotificationCenter;\n\nOptimizelyClient optimizelyClient = optimizelyManager.getOptimizely();\n        int notificationId = optimizelyClient.addDecisionNotificationHandler((DecisionNotification decisionNotification) -> {\n            // Access type on decisionObject to get type of decision\n            Map<String, ?> decisionInfo = decisionNotification.getDecisionInfo();\n            String decisionType = decisionNotification.getType();\n            if (decisionType == \"feature\") {\n                logger.debug(\"Feature access related information: {}\", decisionInfo.toString());\n                // Send data to analytics provider here\n            }\n        });\n\n// Remove a notification listener\noptimizely.getNotificationCenter().removeNotificationListener(notificationId);\n\n// Remove all notification listeners\noptimizely.getNotificationCenter().clearAllNotificationListeners();\n\n",
      "language": "java"
    }
  ]
}
[/block]
The tables below show the information provided to the listener when it is triggered.
[block:parameters]
{
  "data": {
    "0-0": "**type**",
    "0-1": "string",
    "0-2": "- `feature`: Returned when you use the Is Feature Enabled to determine if user has access to one specific feature, or Get Enabled Features method to determine if user has access to multiple features.\n - Is Feature Enabled returns a boolean value.\n - Get Enabled Features returns an array of strings consisting of all features in the project enabled for the user.",
    "1-0": "**decision info**",
    "1-1": "map",
    "2-0": "**user ID**",
    "2-1": "string",
    "3-0": "**attributes**",
    "3-1": "map",
    "1-2": "Key-value map that consists of data corresponding to the decision and based on the `type`.\n\nSee the table below for valid fields and values for each `type`.",
    "h-0": "Field",
    "h-1": "Type",
    "h-2": "Description",
    "2-2": "The user ID.",
    "3-2": "A map of custom key-value string pairs specifying attributes for the user that are used for [audience targeting](doc:target-audiences). Non-string values are only supported in the 3.0 SDK and above."
  },
  "cols": 3,
  "rows": 4
}
[/block]

[block:parameters]
{
  "data": {
    "0-1": "- `feature_key`: String id of the feature.\n- `feature_enabled`: True or false based on whether the feature is enabled for the user.\n- `source`: String denoting how user gained access to the feature. Value is:\n -  `rollout` if the feature became enabled or disabled for the user because of the rollout configuration associated with the feature.",
    "0-0": "**feature**",
    "h-0": "Type",
    "h-1": "Decision Info Values"
  },
  "cols": 2,
  "rows": 1
}
[/block]