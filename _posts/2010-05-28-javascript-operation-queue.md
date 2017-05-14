---
layout: post
title: Javascript Operation Queue
categories:
- Dev
- Palm
- Web Dev
- webOS
tags:
- Mobile Development
- Palm
- palmdev
- Web Development
- webOS
---

When developing for a platform that relies on asynchronous APIs, such as webOS, the application logic frequently will need to block on a given operation prior to executing other dependent components. This may include anything from loading user data after authentication to saving data after initialization of a given data structure among others.

One method of handling this problem is to make the blocker component explicitly aware of the dependent components and the unique interface to each component, which works for simple cases or situations where the dependent to blocking relationship is one-to-one, but this quickly becomes complicated as a number of dependent components grows. In the extreme cases the blocker may have to be aware of significant portions of the system, leading to maintenance concerns.

Alternatively the blocker call can allow dependent components to register their interest in the completion of the operation and upon completion the blocker can simply notify the components on this list in a generic fashion. This allows that components to remain loosely coupled and has the added benefit of allowing for run-time conditional relationships without requiring that the blocker be aware of the state of the dependent.

Implementing such a notification system is fairly straightforward in Javascript: Simply collect waiting callbacks in an array or other structure then executing each upon completion of the blocking call.

### Library

While simple to implement, my experience on[Facebook for webOS](http://www.facebook.com/apps/application.php?id=4620273157) has shown that a library to implement this behavior is worth the initial effort as manually writing nearly identical for loops over callbacks for the umpteenth time becomes tedious and error-prone.

To this end, we developed and open sourced the `OperationQueue` class which provides a common implementation that doesn't require far too many for loops :)

### Usage

To use the `OperationQueue` class you simply need to enqueue your dependent operations using the `queue` API.

`queue` can accept a single function which will be called upon successful completion.

```javascript
    opQueue.queue(function(result) {
        // Do something with the data that we were waiting for!
        console.log("Blocking Operation Completed!");
    });
```

It also accepts an object with any combination of `onSuccess` and `onFailure` fields who will be called for each respective event.

```javascript
    opQueue.queue({
        onSuccess: function(result) {
            // Do something with the data that we were waiting for!
            console.log("Blocking Operation Completed!");
        },
        onFailure: function(result) {
            console.log("Blocking Operation Failed");
        }
    });
```

These calls may occur at anytime. If the blocking operation has already completed then calls to `queue` will result in immediate execution of the queued operation. In this case the result object will not be included.

For the blocking call itself the `getSuccessHandler` and `getFailureHandler` generators will return callback functions that may be used to directly on completion or may be passed as callback handlers to the async API. These methods also accept a function parameter which will be called prior to their completion.

Used directly:

```javascript
opQueue.getSuccessHandler())();
```

As a callback:

```javascript
ServiceRequestWrapper.request('palm://com.palm.preferences/systemProperties', {
        method:"Get",
        parameters:{"key": "com.palm.properties.nduid" },
        onSuccess: opQueue.getSuccessHandler(function(response) {
                Mojo.Log.info("Device ID: %j", response);
            }),
        onFailure: opQueue.getFailureHandler()
    });
```

For more complicated use cases, the `reset` function allows for enabling and disabling queuing at any time. For example, if you need to initially allow all operations to proceed and then block only while a given operation is in progress, the `getSuccessHandler` API may be called immediately after instantiation of the queue and then `reset` called prior to execution of the blocking operation.

### Source:

[Operation queue](http://github.com/palm/webos-samples/tree/master/tipsAndTricks/operation-queue/) is available in the [webos-samples](http://github.com/palm/webos-samples/) repository on github, within the [tipsAndTricks](http://github.com/palm/webos-samples/tree/master/tipsAndTricks/) subproject.

One final note: While this was written for a webOS application, it does not depend on any webOS-specific constructs and may be used in any Javascript environment. To see it in action, check out the [demo](http://demo.incaseofstairs.com/operation-queue/) in any browser!
