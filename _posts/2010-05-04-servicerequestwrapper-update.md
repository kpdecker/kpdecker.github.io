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
<p>
This article is part of my <a href="http://www.incaseofstairs.com/tag/palmdev/">Palm Dev Day</a> summary series as well as a follow up to the <a href="http://www.incaseofstairs.com/2010/04/garbage-collection-gotchas-in-webos/">original</a> service request garbage collection post from last month.</p>
<p>
After reexamining the original <code>ServiceRequestWrapper</code> implementation and the possible use cases, some improvements began to show through:</p>
<ul>
<li>Subscribe requests were not protected from garbage collection after the initial complete callback (Thanks to <a href="http://www.incaseofstairs.com/2010/04/garbage-collection-gotchas-in-webos/#comment-122">Johan</a> for pointing this out)</li>
<li>Requests were not being automatically cancelled on completion</li>
<li>The class did not need to be instantiatable as the 90% case can be handled by a singleton</li>
</ul>
<p>
With these issues in mind I decided that a rewrite was in order to make the class easier to use, as this is what the goal was in the first place :).</p>
<h3>Non-Subscription Requests</h3>
<p>
Usage for non-subscription requests now involves a single call, <code>ServiceRequestWrapper.request</code> that is a "fire and forget" call meaning that cleanup is completely automated.</p>
<p>For example a call to determine the device ID can be done as follows:<br />
[javascript]<br />
    ServiceRequestWrapper.request('palm://com.palm.preferences/systemProperties', {<br />
            method:&quot;Get&quot;,<br />
            parameters:{&quot;key&quot;: &quot;com.palm.properties.nduid&quot; },<br />
            onSuccess: function(response) {<br />
                Mojo.Log.info(&quot;Device ID: %j&quot;, response);<br />
            }<br />
      });<br />
[/javascript]</p>
<p>
Note that are no special requirements to cleanup the request object for these types of calls. Upon completion the request object will be cleaned from both the <code>ServiceRequestWrapper</code> data structures as well as any system level data structures.</p>
<h3>Subscription Requests</h3>
<p>
The subscription case is not as simple as the framework can not reliably determine when the request is complete and future results are no longer desired. In order to reliably cleanup subscription requests <code>ServiceRequestWrapper</code> places the cleanup responsibility on the caller, via the <code>cancel</code> method, much in the same way as the <a href="http://developer.palm.com/index.php?option=com_content&view=article&id=1867&Itemid=256">Mojo.Service.Request</a> API.</p>
<p>
In practice this is not much harder than dealing with the single case. The following example monitors the system preferences for two changes to an arbitrary preference and then cancels any further action on that subscription.</p>
<p>[javascript]<br />
var count = 0;<br />
ServiceRequestWrapper.request('palm://com.palm.systemservice', {<br />
    method:&quot;getPreferences&quot;,<br />
    parameters:{ keys: [ &quot;food&quot; ], subscribe: true },<br />
    onSuccess: function(response, request) {<br />
        Mojo.Log.info(&quot;Preference changed: %j&quot;, response);<br />
        count++;<br />
        if (count === 2) {<br />
            request.cancel();<br />
        }<br />
    }<br />
});<br />
[/javascript]</p>
<p>
The <code>request</code> API also returns the a request object, which is identical to the 2nd parameter passed to callbacks, for those that need to cancel the request outside of the scope of a callback.</p>
<p>[javascript]<br />
var subsRequest = ServiceRequestWrapper.request('palm://com.palm.systemservice', {<br />
    method:&quot;getPreferences&quot;,<br />
    parameters:{ keys: [ &quot;food&quot; ], subscribe: true },<br />
    onSuccess: function(response, request) {<br />
        Mojo.Log.info(&quot;Preference changed: %j&quot;, response);<br />
    }<br />
});</p>
<p>// And then a miracle occurs</p>
<p>subRequest.cancel();<br />
[/javascript]</p>
<h3>Source</h3>
<p>The updated library is available on github in the <a href="http://github.com/palm/webos-samples/tree/master/tipsAndTricks/service-request-wrapper/">palm/webos-samples</a> repository.</p>
