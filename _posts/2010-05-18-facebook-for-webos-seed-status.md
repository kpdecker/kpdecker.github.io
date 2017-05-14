---
layout: post
title: 'Facebook for webOS: Seed Status'
categories:
- Dev
- Palm
- webOS
tags:
- Mobile Development
- Palm
- webOS
---

With the 1.2.1 release of Facebook for webOS, we are now officially supporting the ability to seed status updates via an application manager launch. This allows for applications that do not wish to interact directly with the Facebook APIs to provide Facebook status posts with minimal effort.

All that is required is a single service request to the application manager.

```javascript
function seedFacebookStatus(text) {
    ServiceRequestWrapper.request('palm://com.palm.applicationManager', {
        method: 'launch',
        parameters: {
            id: `com.palm.app.facebook`,
            params: { status: text }
        }
    });
}
```

This call will launch the Facebook application, open to the new stream, and populate the update status control in the Facebook application. Once populated the user may edit the message as they please and submit.

A sample project is available on [github](http://github.com/kpdecker/webos-fbSeedStatus).

Are there any other features that you would like to see via launch APIs in the Facebook application or for Facebook APIs in general on webOS? Feel free to leave a comment on this post with any ideas that you may have.
