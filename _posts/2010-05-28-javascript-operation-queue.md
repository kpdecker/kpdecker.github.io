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
<p>
When developing for a platform that relies on asynchronous APIs, such as webOS, the application logic frequently will need to block on a given operation prior to executing other dependent components. This may include anything from loading user data after authentication to saving data after initialization of a given data structure among others.</p>
<p>
One method of handling this problem is to make the blocker component explicitly aware of the dependent components and the unique interface to each component, which works for simple cases or situations where the dependent to blocking relationship is one-to-one, but this quickly becomes complicated as a number of dependent components grows. In the extreme cases the blocker may have to be aware of significant portions of the system, leading to maintenance concerns.</p>
<p>
Alternatively the blocker call can allow dependent components to register their interest in the completion of the operation and upon completion the blocker can simply notify the components on this list in a generic fashion. This allows that components to remain loosely coupled and has the added benefit of allowing for run-time conditional relationships without requiring that the blocker be aware of the state of the dependent.</p>
<p>
Implementing such a notification system is fairly straightforward in Javascript: Simply collect waiting callbacks in an array or other structure then executing each upon completion of the blocking call.</p>
<h3>Library</h3>
<p>
While simple to implement, my experience on<a href="http://www.facebook.com/apps/application.php?id=4620273157">Facebook for webOS</a> has shown that a library to implement this behavior is worth the initial effort as manually writing nearly identical for loops over callbacks for the umpteenth time becomes tedious and error-prone.</p>
<p>
To this end, we developed and open sourced the <code>OperationQueue</code> class which provides a common implementation that doesn't require far too many for loops :)</p>
<h3>Usage</h3>
<p>To use the <code>OperationQueue</code> class you simply need to enqueue your dependent operations using the <code>queue</code> API.</p>
<p><code>queue</code> can accept a single function which will be called upon successful completion.</p>
<p>[javascript]<br />
    opQueue.queue(function(result) {<br />
        // Do something with the data that we were waiting for!<br />
        console.log(&quot;Blocking Operation Completed!&quot;);<br />
    });<br />
[/javascript]</p>
<p>It also accepts an object with any combination of <code>onSuccess</code> and <code>onFailure</code> fields who will be called for each respective event.<br />
[javascript]<br />
    opQueue.queue({<br />
        onSuccess: function(result) {<br />
            // Do something with the data that we were waiting for!<br />
            console.log(&quot;Blocking Operation Completed!&quot;);<br />
        },<br />
        onFailure: function(result) {<br />
            console.log(&quot;Blocking Operation Failed&quot;);<br />
        }<br />
    });<br />
[/javascript]</p>
<p>
These calls may occur at anytime. If the blocking operation has already completed then calls to <code>queue</code> will result in immediate execution of the queued operation. In this case the result object will not be included.</p>
<p>
For the blocking call itself the <code>getSuccessHandler</code> and <code>getFailureHandler</code> generators will return callback functions that may be used to directly on completion or may be passed as callback handlers to the async API. These methods also accept a function parameter which will be called prior to their completion.</p>
<p>Used directly:<br />
[javascript light="true"](opQueue.getSuccessHandler())();[/javascript]</p>
<p>As a callback:<br />
[javascript]<br />
ServiceRequestWrapper.request('palm://com.palm.preferences/systemProperties', {<br />
        method:&quot;Get&quot;,<br />
        parameters:{&quot;key&quot;: &quot;com.palm.properties.nduid&quot; },<br />
        onSuccess: opQueue.getSuccessHandler(function(response) {<br />
                Mojo.Log.info(&quot;Device ID: %j&quot;, response);<br />
            }),<br />
        onFailure: opQueue.getFailureHandler()<br />
    });<br />
[/javascript]</p>
<p>
For more complicated use cases, the <code>reset</code> function allows for enabling and disabling queuing at any time. For example, if you need to initially allow all operations to proceed and then block only while a given operation is in progress, the <code>getSuccessHandler</code> API may be called immediately after instantiation of the queue and then <code>reset</code> called prior to execution of the blocking operation.</p>
<h3>Source:</h3>
<p>
<a href="http://github.com/palm/webos-samples/tree/master/tipsAndTricks/operation-queue/">Operation queue</a> is available in the <a href="http://github.com/palm/webos-samples/">webos-samples</a> repository on github, within the <a href="http://github.com/palm/webos-samples/tree/master/tipsAndTricks/">tipsAndTricks</a> subproject.</p>
<p>
One final note: While this was written for a webOS application, it does not depend on any webOS-specific constructs and may be used in any Javascript environment. To see it in action, check out the <a href="http://demo.incaseofstairs.com/operation-queue/">demo</a> in any browser!</p>
