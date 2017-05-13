---
layout: post
title: Garbage Collection Gotchas in webOS
categories:
- Palm
- webOS
tags:
- debugging
- Mobile Development
- Palm
- webOS
---
<p>
Making the jump to mobile development from desktop and server development can lead to quite a few gotchas due to the resource constraints inherent to mobile environments. This became very clear to me today while debugging an intermittent failure in the Facebook application's news and notifications scenes.</p>
<p>
Like every other debugging session for intermittent issues there were quite a few choice words used in the process and many dead ends, but eventually it became apparent that some vital <code><a href="https://developer.palm.com/index.php?option=com_content&amp;view=article&amp;id=1867&amp;Itemid=256#.Request">Mojo.Service.Request</a></code> service requests were not calling any of their notification callbacks. Expecting the <code>onComplete</code> callback to occur at the very least, I was very perplexed until I spoke with some of my esteemed colleagues at Palm and they pointed me to the garbage collector as the possible culprit for this situation.</p>
<p>
The code in question was instantiating <em>Request</em> objects but not maintaining references to these objects. This is fine up until the point that the garbage collector runs and the request object as well as all callbacks are collected. At this point these requests could no longer notify the application of their completion, blocking further processing of the queues associated with these requests.</p>
<p>
After storing the instantiated <code>Request</code> objects until completion all of the calls that were previously disappearing into the ether began to return as expected.</p>
<p>
The moral of the story is that you need to be very careful about what references are maintained when working with garbage-collected languages in memory-constrained environments. If you want something to stay around, like callback methods, you need to be certain that it is referenced somewhere. To ease this task for <code>Mojo.Service.Request</code> calls (<code>controller.serviceRequest</code> calls are safe as the controller maintains a reference to the request object or cancels the request prior to popping the scene), we implemented the following wrapper:</p>

{% highlight javascript %}
var ServiceRequestWrapper = Class.create({
    initialize: function() {
        this.requestId = 0;
        this.requests = {};
    },

    request: function(url, optionsIn, resubscribe) {
        Mojo.Log.info(&quot;Service request wrapper url: '%s' method: '%s' CALLED&quot;, url, optionsIn.method);
        var options = Object.clone(optionsIn),
            requestId = this.requestId++;
        options.onComplete = this.completeHandler.bind(this, url, optionsIn, requestId);

        this.requests[requestId] = new Mojo.Service.Request(url, options, resubscribe);
        return this.requests[requestId];
    },

    completeHandler: function(url, optionsIn, requestId, response) {
        Mojo.Log.info(&quot;Service request wrapper url: '%s' method: '%s' requestId: %d COMPLETE&quot;, url, optionsIn.method, requestId);
        delete this.requests[requestId];

        optionsIn.onComplete &amp;&amp; optionsIn.onComplete(response);
    }
});
{% endhighlight %}

By instantiating this on a location that will not be collected during the application's lifetime and using the request method rather than direct <code>Mojo.Service.Request</code> calls for all requests that the result is necessary you can avoid this problematic scenario as well as save the choice words for a later time :)</p>
