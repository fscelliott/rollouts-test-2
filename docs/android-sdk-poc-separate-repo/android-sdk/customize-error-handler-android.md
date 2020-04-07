---
title: "Customize error handler"
slug: "customize-error-handler-android"
hidden: false
createdAt: "2019-08-21T20:59:01.323Z"
updatedAt: "2019-08-21T20:59:55.627Z"
---
You can provide your own custom **error handler** logic to standardize across your production environment. 

This error handler is called when an unknown feature key is referenced.

See the code example below. If the error handler is not overridden, a no-op error handler is used by default.
[block:code]
{
  "codes": [
    {
      "code": "import com.optimizely.ab.android.sdk.OptimizelyManager;\nimport com.optimizely.ab.error.ErrorHandler;\n\n// Default handler that raises exceptions\nErrorHandler errorHandler = new RaiseExceptionErrorHandler();\n\n// Build a manager\noptimizelyManager = OptimizelyManager.builder()\n  .withSDKKey(\"SDK_KEY_HERE\")\n  .withEventDispatchInterval(60L * 10L)\n  .withErrorHandler(errorHandler)\n  .withDatafileDownloadInterval(60L * 10L)\n  .build(getApplicationContext());\n\n",
      "language": "java"
    }
  ]
}
[/block]