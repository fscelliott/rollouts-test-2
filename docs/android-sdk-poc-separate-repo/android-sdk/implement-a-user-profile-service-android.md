---
title: "Implement a user profile service"
slug: "implement-a-user-profile-service-android"
hidden: false
createdAt: "2019-09-11T13:37:59.098Z"
updatedAt: "2020-01-31T18:21:29.763Z"
---
Use a **User Profile Service** to persist information about your users and ensure variation assignments are sticky. For example, if you are working on a backend website, you can create an implementation that reads and saves user profiles from a Redis or memcached store. 

The Android SDK defaults to a User Profile Service that stores this state directly on the device. See the [Android SDK User Profile Service](https://github.com/optimizely/android-sdk/blob/master/user-profile/src/main/java/com/optimizely/ab/android/user_profile/DefaultUserProfileService.java).

Use `manager.userProfileService.lookup` to read a customer’s user profile.
[block:api-header]
{
  "title": "No op in Android"
}
[/block]
In Android, the default User Profile Service is also sticky. This can be overruled by disabling `UserProfileService` in your Android app. To do so, define a `CustomUserProfileService` class inheriting on our `DefaultUserProfileService class`, but overriding the `lookupmethod` to always return null. See the example below:
[block:code]
{
  "codes": [
    {
      "code": "public class CustomUserProfileService extends DefaultUserProfileService {\n @Override\n public Map < String, Object > lookup(String userId) {\n  return null;\n }\n}\n\n",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]
Pass this to your OptimizelyManager:
[block:code]
{
  "codes": [
    {
      "code": "CustomUserProfileService customUserProfileService = new CustomUserProfileService();\noptimizelyManager = OptimizelyManager.builder(PROJECT_ID).withUserProfileService(customUserProfileService).build(getApplicationContext());\n\n",
      "language": "java",
      "name": "Android"
    }
  ]
}
[/block]
With this custom implementation, the bucketing will no longer be sticky when the traffic allocation changes.
[block:api-header]
{
  "title": "Implement a service"
}
[/block]
Your own User Profile Service should expose two functions with the following signatures:
* `lookup`: Takes a user ID string and returns a user profile matching the schema below.
* `save`: Takes a user profile and persists it.

If you want to use the User Profile Service purely for tracking purposes and not sticky bucketing, you can implement only the `save` method (always return `nil` from `lookup`).

The code example below shows the JSON schema for the user profile object.

Use `experiment_bucket_map` to override the default bucketing behavior and define an alternate experiment variation for a given user. For each experiment that you want to override, add an object to the map. Use the experiment ID as the key and include a `variation_id` property that specifies the desired variation. If there isn't an entry for an experiment, then the default bucketing behavior persists.

In the example below, `^[a-zA-Z0-9]+$` is the experiment ID.
[block:code]
{
  "codes": [
    {
      "code": "{\n  \"title\": \"UserProfile\",\n  \"type\": \"object\",\n  \"properties\": {\n    \"user_id\": {\"type\": \"string\"},\n    \"experiment_bucket_map\": {\"type\": \"object\",\n                              \"patternProperties\": {\n                                 \"^[a-zA-Z0-9]+$\": {\"type\": \"object\",\n                                                    \"properties\": {\"variation_id\": {\"type\":\"string\"}},\n                                                    \"required\": [\"variation_id\"]}\n                               }\n                             }\n  },\n  \"required\": [\"user_id\", \"experiment_bucket_map\"]\n}\n\n",
      "language": "json"
    }
  ]
}
[/block]
The SDK uses the User Profile Service you provide to override Optimizely's default bucketing behavior in cases when an experiment assignment has been saved.

For both Swift and Android apps, the User Profile Service will persist variation assignments across app updates. However, the User Profile Service will not persist variation assignments across app re-installs.

When implementing your own User Profile Service, we recommend loading the user profiles into the User Profile Service on initialization and avoiding performing expensive, blocking lookups on the lookup function to minimize the performance impact of incorporating the service.

When implementing in a multi-server or stateless environment, we suggest using this interface with a backend like [Cassandra](http://cassandra.apache.org/) or [Redis](https://redis.io/). You can decide how long you want to keep your sticky bucketing around by configuring these services.