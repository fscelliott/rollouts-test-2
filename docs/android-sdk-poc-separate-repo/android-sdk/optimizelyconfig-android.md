---
title: "OptimizelyConfig"
slug: "optimizelyconfig-android"
hidden: false
createdAt: "2020-01-17T19:10:56.542Z"
updatedAt: "2020-01-31T18:21:19.284Z"
---
[block:api-header]
{
  "title": "Overview"
}
[/block]
Full Stack SDKs open a well-defined set of public APIs, hiding all implementation details. However, some clients may need access to project configuration data within the "datafile". 

In this document, we extend our public APIs to define data models and access methods, which clients can use to access project configuration data. 

[block:api-header]
{
  "title": "OptimizelyConfig API"
}
[/block]

A public configuration data model (OptimizelyConfig) is defined below as a structured format of static Optimizely Project data.

OptimizelyConfig can be accessed from OptimizelyClient (top-level) with this public API call:
[block:code]
{
  "codes": [
    {
      "code": "public OptimizelyConfig getOptimizelyConfig();",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]
`getOptimizelyConfig` returns
an `OptimizelyConfig` instance which include a datafile revision number, all experiments, and feature flags mapped by their key values.
[block:callout]
{
  "type": "info",
  "title": "Note",
  "body": "When the SDK datafile is updated (the client can add a notification listener for `OPTIMIZELY_CONFIG_UPDATE` to get notified), the client is expected to call the method to get the updated OptimizelyConfig data. See examples below."
}
[/block]

[block:code]
{
  "codes": [
    {
      "code": "// OptimizelyConfig is an object describing the current project configuration data \npublic class OptimizelyConfig {\n    Map<String, OptimizelyExperiment> experimentsMap;\n    Map<String, OptimizelyFeature> featuresMap;\n    String revision;\n}\n\n// OptimizelyFeature is an object describing a feature\npublic class OptimizelyFeature {\n    String id;\n    String key;\n    Map<String, OptimizelyExperiment> experimentsMap;\n    Map<String, OptimizelyVariable> variablesMap;\n}\n\n// OptimizelyExperiment is an object describing a experiment\npublic class OptimizelyExperiment {\n    String id;\n    String key;\n    Map<String, OptimizelyVariation> variationsMap;\n}\n\n// OptimizelyVariation is an object describing a variation\npublic class OptimizelyVariation {\n    String id;\n    String key;\n    Boolean featureEnabled;\n\n    Map<String, OptimizelyVariable> variablesMap;\n}\n\n// OptimizelyVariable is an object describing a Variable\npublic class OptimizelyVariable {\n    String id;\n    String key;\n    String type;\n    String value;\n}\n",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]

[block:api-header]
{
  "title": "Examples"
}
[/block]
OptimizelyConfig can be accessed from OptimizelyClient (top-level) like this:

[block:code]
{
  "codes": [
    {
      "code": "OptimizelyConfig config = optimizelyClient.getOptimizelyConfig()\n\n// all experiments\nfor (String expKey: config.getExperimentsMap().keySet()) {\n   OptimizelyExperiment experiment = config.getExperimentsMap().get(expKey);\n   Map<String, OptimizelyVariation> variationsMap = experiment.getVariationsMap();\n   // all variations for experiment\n   for (String variationKey: variationsMap.keySet()) {\n       OptimizelyVariation variation = variationsMap.get(variationKey);\n       // all variables for a variation\n       Map<String, OptimizelyVariable> optimizelyVariableMap = variation.getVariablesMap();\n       for (String variableKey: optimizelyVariableMap.keySet()) {\n           OptimizelyVariable variable = optimizelyVariableMap.get(variableKey);\n          \n           // use variable data here...\n       }\n   }\n}\n\n// all features\nfor (String featureKey: config.getFeaturesMap().keySet()) {\n   OptimizelyFeature experiment = config.getFeaturesMap().get(featureKey);\n   Map<String, OptimizelyExperiment> experimentsMap = experiment.getExperimentsMap();\n   // feature experiments\n   Set<String> experimentKeys = experimentsMap.keySet();\n\n   // use experiments and other feature data here...\n}\n\n// listen to OPTIMIZELY_CONFIG_UPDATE to get updated data\noptimizelyClient.getNotificationCenter().addNotificationHandler(UpdateConfigNotification.class, handler -> {\n  OptimizelyConfig newConfig = optimizelyClient.getOptimizelyConfig();\n});\n",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]