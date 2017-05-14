---
layout: post
title: ServiceRequestWrapper Update
categories:
- Dev
- Palm
- webOS
tags:
- Mobile Development
- Palm
- palmdev
- webOS
---

This article is part of my [Palm Dev Day](http://www.incaseofstairs.com/tag/palmdev/) summary series as well as a follow up to the [original](http://www.incaseofstairs.com/2010/04/garbage-collection-gotchas-in-webos/) service request garbage collection post from last month.

After reexamining the original `ServiceRequestWrapper` implementation and the possible use cases, some improvements began to show through:


- Subscribe requests were not protected from garbage collection after the initial complete callback (Thanks to [Johan](http://www.incaseofstairs.com/2010/04/garbage-collection-gotchas-in-webos/#comment-122) for pointing this out)
- Requests were not being automatically cancelled on completion
- The class did not need to be instantiatable as the 90% case can be handled by a singleton

With these issues in mind I decided that a rewrite was in order to make the class easier to use, as this is what the goal was in the first place :).

### Non-Subscription Requests

Usage for non-subscription requests now involves a single call, `ServiceRequestWrapper.request` that is a "fire and forget" call meaning that cleanup is completely automated.

For example a call to determine the device ID can be done as follows:

```javascript
    ServiceRequestWrapper.request('palm://com.palm.preferences/systemProperties', {
            method:"Get",
            parameters:{"key": "com.palm.properties.nduid" },
            onSuccess: function(response) {
                Mojo.Log.info("Device ID: %j", response);
            }
      });
```

Note that are no special requirements to cleanup the request object for these types of calls. Upon completion the request object will be cleaned from both the `ServiceRequestWrapper` data structures as well as any system level data structures.

### Subscription Requests

The subscription case is not as simple as the framework can not reliably determine when the request is complete and future results are no longer desired. In order to reliably cleanup subscription requests `ServiceRequestWrapper` places the cleanup responsibility on the caller, via the `cancel` method, much in the same way as the [Mojo.Service.Request](http://developer.palm.com/index.php?option=com_content&view=article&id=1867&Itemid=256) API.

In practice this is not much harder than dealing with the single case. The following example monitors the system preferences for two changes to an arbitrary preference and then cancels any further action on that subscription.

```javascript
var count = 0;
ServiceRequestWrapper.request('palm://com.palm.systemservice', {
    method:"getPreferences",
    parameters:{ keys: [ "food" ], subscribe: true },
    onSuccess: function(response, request) {
        Mojo.Log.info("Preference changed: %j", response);
        count++;
        if (count === 2) {
            request.cancel();
        }
    }
});
```

The `request` API also returns the a request object, which is identical to the 2nd parameter passed to callbacks, for those that need to cancel the request outside of the scope of a callback.

```javascript
var subsRequest = ServiceRequestWrapper.request('palm://com.palm.systemservice', {
    method:"getPreferences",
    parameters:{ keys: [ "food" ], subscribe: true },
    onSuccess: function(response, request) {
        Mojo.Log.info("Preference changed: %j", response);
    }
});

// And then a miracle occurs

subRequest.cancel();
```

### Source

The updated library is available on github in the [palm/webos-samples](http://github.com/palm/webos-samples/tree/master/tipsAndTricks/service-request-wrapper/) repository.
