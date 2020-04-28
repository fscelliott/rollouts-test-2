---
title: "Install SDK"
slug: "install-sdk-android"
hidden: false
createdAt: "2019-08-21T20:51:44.137Z"
updatedAt: "2019-08-21T20:54:16.607Z"
---
The Android SDK packages are available on Bintray [android-sdk](https://bintray.com/optimizely/optimizely/optimizely-sdk-android) using JCenter repository.

To add the `android-sdk` and all modules to your project, include this line in your app's `build.gradle` in the `dependencies` block:

```
implementation 'com.optimizely.ab:android-sdk:3.0.0'
```
[block:code]
{
  "codes": [
    {
      "code": "repositories {\n  jcenter()\n}\n\ndependencies {\n  compile ('com.optimizely.ab:android-sdk:3.0.0') {\n      // exclude default implementation of slf4j if you want to provide \n      // your own.  Multidex can fail if you have more than one \n      // implementation present. If you are having a multidex error\n      // this is most likely the issue.\n      exclude group: 'com.noveogroup.android', module:'android-logger'\n  }\n}\n\n",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]
The full source code is at https://github.com/optimizely/android-sdk.