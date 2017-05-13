---
layout: post
title: 'border-image-generator: Local File Access'
categories:
- border-image-generator
- Web Dev
tags:
- border-image-generator
- HTML5
- Web Development
---
<p>
This article covers the techniques used to implement the local file access feature that is included, along with tiling support, alternate styling, and parameter optimization, in the most recent push to <a href="http://border-image.com">border-image-generator</a>.</p>
<p>
One of the biggest short comings that I felt like border-image-generator was that all files had to be posted on a HTTP server due to restrictions on the access to the <code>file://</code> protocol. In my personal workflow this was somewhat of a pain, so I set out to find a way around this issue.</p>
<p>
During the design of this feature, I quickly decided that all data must remain on the client as I did not want to upgrade my hosting service to include more bandwidth, felt like responsiveness could be a concern, and do not want to tangle with the potentially messy privacy issues that could arrise from storing user generated images on my server.</p>
<p>
While this sounded like a daunting task, it turns out that this was possible, in sufficiently advanced browsers, using a variety of HTML5 techniques, including <a href="http://en.wikipedia.org/wiki/Data_URI_scheme">Data URIs</a>, the <a href="http://www.w3.org/TR/FileAPI/">File API</a> and the <a href="http://www.w3.org/TR/webstorage/">Web Storage API</a>.</p>
<h3>Display</h3>
<p>
The problem of displaying the image was the easiest to solve as all of the targeted browsers support data URIs as part of the <code>border-image</code> property. This allowed the application to generate URLs like the following</p>
<p>[css]<br />
    border-width: 46px;<br />
    border-image: url(&quot;data:image/png;base64,iVBORw0KGgoAAA....&quot;) 46 stretch;<br />
[/css]</p>
<p>
And operate as expected. Data URIs have been covered fairly extensively elsewhere, so I will leave this to the reader to investigate.</p>
<h3>Reading</h3>
<p>
With the actual display issue solvled, there was still the non-trivial issue of actually loading the content. My initial investigations involved Flash, which provides <a href="http://help.adobe.com/en_US/AS3LCR/Flash_10.0/flash/net/FileReference.html#load()">FileReference.load</a> API which allowing for this functionality, but under the version I was testing on this API is only usable if invoked in response to user input. Being a HTML guy and lacking functional knowledge of the Flash development process I quickly ruled this technique out.</p>
<p>
Continuing my research I came across an <a href="https://developer.mozilla.org/en/Using_files_from_web_applications">article</a> on MDC that covered this exact use case, using the draft File API specification. This worked like a charm, even exposing the ability to read the image contents directly into a data URI.</p>
<p>The API is very clean for use cases such as this:</p>
<p>[javascript]<br />
function loadFile(file) {<br />
    var reader = new FileReader();<br />
    reader.onload = function(event) {<br />
         updateImageURI(file.name, reader.result);<br />
     };<br />
     reader.readAsDataURL(file);<br />
}<br />
[/javascript]</p>
<p>
Where the <code>file</code> variable above is a <code>File</code> object retrieved from a [html light="true"]&lt;input type=&quot;file&quot;&gt;[/html] or the <code>dataTransfer</code> object passed to the <code>drop</code> html event.</p>
<p>[javascript]<br />
        $(&quot;body&quot;).bind(&quot;dragenter dragover&quot;, function(event) {<br />
            // We have to cancel these events or we will not recieve the drop event<br />
            event.preventDefault();<br />
            event.stopPropagation();<br />
        });<br />
        $(&quot;body&quot;).bind(&quot;drop&quot;, function(event) {<br />
            event.preventDefault();<br />
            event.stopPropagation();<br />
            var dataTransfer = event.originalEvent.dataTransfer,<br />
                file = dataTransfer.files[0];</p>
<p>            loadFile(file);<br />
        });<br />
        $(&quot;#localImage&quot;).bind(&quot;change&quot;, function(event) {<br />
            var file = this.files[0];</p>
<p>            loadFile(file);<br />
        });<br />
[/javascript]</p>
<p>
This unfortunately is not without it's issues. The File API is very much bleeding edge at this point and support is somewhat limited. As of this writing Firefox is the only browser which features this in production (Version 3.6). Support <a href="http://trac.webkit.org/changeset/59162">landed</a> in the WebKit trunk literally earlier this month and can be used in their nightlies, but so far has not made it into any production releases.</p>
<p>
The site is currently designed to progressively enhance as it detects support for the File API, so no need to worry about being left out of the site as a whole if you are not on one of these browsers yet.</p>
<h3>History</h3>
<p>
After loading the content using the File API, the original implementation utilized the same #hash storage method for the data URIs, but this proved to be problematic as these strings can become quite large and interacting with these URLs was unwieldily. Needing another data store and being forced to maintain a cache of all local images due to the security model employed by the File API, we were left the options of storing all data in Javascript space or using the new Web Storage APIs implemented as part of HTML5.</p>
<p>
Examining both options it seemed the the best course was to utilize the <code>sessionStorage</code> object when available and fail over to the javascript data model when not.</p>
<p>[javascript]<br />
    // Check for browser support of session storage and that it is accessible<br />
    // This may be inaccessible under certain contexts such as file://<br />
    function supportsSessionStorage() {<br />
        try {<br />
            return !!window.sessionStorage;<br />
        } catch (err) {<br />
            return false;<br />
        }<br />
    }<br />
    var localDataBinding = (function() {<br />
        if (supportsSessionStorage()) {<br />
            // If they support FileReader they really should support storage... but who knows (With the exception of file://)<br />
            return {<br />
                storeImage: function(name, data) {<br />
                    var entryId = (parseInt(sessionStorage[&quot;imageList-count&quot;])||0)+1;<br />
                    sessionStorage[&quot;imageList-count&quot;] = entryId;<br />
                    sessionStorage[&quot;imageList-src-&quot; + entryId] = data;<br />
                    sessionStorage[&quot;imageList-display-&quot; + entryId] = name;<br />
                    return entryId;<br />
                },<br />
                getImage: function(entryId) {<br />
                    return { src: sessionStorage[&quot;imageList-src-&quot; + entryId], displayName: sessionStorage[&quot;imageList-display-&quot; + entryId] };<br />
                }<br />
            };<br />
        } else {<br />
            // Fail over to plain js structures, meaing that refresh, etc will cause failures.<br />
            var cache = [];<br />
            return {<br />
                storeImage: function(name, data) {<br />
                    cache.push({ src: data, displayName: name });<br />
                    return cache.length-1;<br />
                },<br />
                getImage: function(entryId) {<br />
                    return cache[entryId];<br />
                }<br />
            };<br />
        }<br />
    })();<br />
[/javascript]</p>
<p>
Working with this API it seems as though it is still somewhat rough. The vendor documentation all state that supported datatypes are only string values (<a href="https://developer.mozilla.org/en/dom/storage">Mozilla</a>, <a href="http://developer.apple.com/safari/library/documentation/AppleApplications/Reference/WebKitDOMRef/Storage_idl/Classes/Storage/index.html">WebKit</a>), but the spec implies that this will change be final release and allow for storage of a much broader set of datatypes.</p>
<p>
A consequence of this design is that boomark and URL sharing for local files is not a possibility. For users who need to share or store  application states the image in question will need to be stored on a HTTP server and accessed by this route.</p>
<p>
These changes as well as some incremental changes have been pushed live on <a href="http://border-image.com">border-image.com</a> and are available in the github <a href="http://github.com/kpdecker/border-image-generator">repository</a>.</p>
