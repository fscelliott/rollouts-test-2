---
title: "Customize logger"
slug: "customize-logger-android"
hidden: false
createdAt: "2019-08-21T20:58:04.967Z"
updatedAt: "2019-08-21T20:58:26.467Z"
---
The **logger** logs information about your experiments to help you with debugging. You can customize where log information is sent and what kind of information is tracked.

For the Android SDK, you can use our Android SLF4J [logger](https://github.com/noveogroup/android-logger). Logging verbosity can be controlled via the `android-logger.properties` file. This file can be placed in `src/main/resources`. You can also include a copy in `src/release/resources` with different settings for the build you release to the Play Store. Read about [advanced configuration options](https://github.com/noveogroup/android-logger).

To improve your experience setting up the SDK and configuring your production environment, we recommend that you pass in a logger for your Optimizely client. See the code example below. 
[block:code]
{
  "codes": [
    {
      "code": "# android-logger.properties example (JSON)\n\n# Java Core SDK\nlogger.com.optimizely.ab.Optimizely=DEBUG:Optly.core\nlogger.com.optimizely.ab.annotations=DEBUG:Optly.annotations\nlogger.com.optimizely.ab.bucketing=DEBUG:Optly.bucketing\nlogger.com.optimizely.ab.config=DEBUG:Optly.config\nlogger.com.optimizely.ab.error=DEBUG:Optly.error\nlogger.com.optimizely.ab.event=DEBUG:Optly.event\nlogger.com.optimizely.ab.internal=DEBUG:Optly.internal\n\n# Android SDK\nlogger.com.optimizely.ab.android.sdk=DEBUG:Optly.androidSdk\nlogger.com.optimizely.ab.android.event_handler=DEBUG:Optly.eventHandler\nlogger.com.optimizely.ab.android.shared=DEBUG:Optly.shared\nlogger.com.optimizely.ab.android.user_profile=DEBUG:Optly.userProfile\n\n# Disable most SDK logs by commenting all other lines and uncommenting below\n# logger.com.optimizely.ab=ASSERT:Optly\n\n",
      "language": "json"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Log levels"
}
[/block]
The table below lists the log levels for the Android SDK.
[block:parameters]
{
  "data": {
    "h-0": "Log Level",
    "h-1": "Explanation",
    "0-0": "**ERROR**",
    "0-1": "Events that prevent feature flags from functioning correctly (for example, invalid datafile in initialization and invalid feature keys) are logged. The user can take action to correct.",
    "1-0": "**WARN**",
    "1-1": "Events that don't prevent feature flags from functioning correctly, but can have unexpected outcomes (for example, future API deprecation, logger or error handler are not set properly, and nil values from getters) are logged.",
    "2-0": "**INFO**",
    "2-1": "Events of significance (for example, activate started, activate succeeded, tracking started, and tracking succeeded) are logged. This is helpful in showing the lifecycle of an API call.",
    "3-1": "Any information related to errors that can help us debug the issue (for example, the feature flag is not running, user is not included in the rollout) are logged.",
    "3-0": "**DEBUG** "
  },
  "cols": 2,
  "rows": 4
}
[/block]