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

This article covers the techniques used to implement the local file access feature that is included, along with tiling support, alternate styling, and parameter optimization, in the most recent push to [border-image-generator](http://border-image.com).

One of the biggest short comings that I felt like border-image-generator was that all files had to be posted on a HTTP server due to restrictions on the access to the `file://` protocol. In my personal workflow this was somewhat of a pain, so I set out to find a way around this issue.

During the design of this feature, I quickly decided that all data must remain on the client as I did not want to upgrade my hosting service to include more bandwidth, felt like responsiveness could be a concern, and do not want to tangle with the potentially messy privacy issues that could arrise from storing user generated images on my server.

While this sounded like a daunting task, it turns out that this was possible, in sufficiently advanced browsers, using a variety of HTML5 techniques, including [Data URIs](http://en.wikipedia.org/wiki/Data_URI_scheme), the [File API](http://www.w3.org/TR/FileAPI/) and the [Web Storage API](http://www.w3.org/TR/webstorage/).

### Display

The problem of displaying the image was the easiest to solve as all of the targeted browsers support data URIs as part of the `border-image` property. This allowed the application to generate URLs like the following

```css
    border-width: 46px;
    border-image: url("data:image/png;base64,iVBORw0KGgoAAA....") 46 stretch;
```

And operate as expected. Data URIs have been covered fairly extensively elsewhere, so I will leave this to the reader to investigate.

### Reading

With the actual display issue solvled, there was still the non-trivial issue of actually loading the content. My initial investigations involved Flash, which provides [FileReference.load](http://help.adobe.com/en_US/AS3LCR/Flash_10.0/flash/net/FileReference.html#load()) API which allowing for this functionality, but under the version I was testing on this API is only usable if invoked in response to user input. Being a HTML guy and lacking functional knowledge of the Flash development process I quickly ruled this technique out.

Continuing my research I came across an [article](https://developer.mozilla.org/en/Using_files_from_web_applications) on MDC that covered this exact use case, using the draft File API specification. This worked like a charm, even exposing the ability to read the image contents directly into a data URI.

The API is very clean for use cases such as this:

```javascript
function loadFile(file) {
    var reader = new FileReader();
    reader.onload = function(event) {
         updateImageURI(file.name, reader.result);
     };
     reader.readAsDataURL(file);
}
```

Where the `file` variable above is a `File` object retrieved from a `<input type="file">` or the `dataTransfer` object passed to the `drop` html event.

```javascript
        $("body").bind("dragenter dragover", function(event) {
            // We have to cancel these events or we will not recieve the drop event
            event.preventDefault();
            event.stopPropagation();
        });
        $("body").bind("drop", function(event) {
            event.preventDefault();
            event.stopPropagation();
            var dataTransfer = event.originalEvent.dataTransfer,
                file = dataTransfer.files[0];

            loadFile(file);
        });
        $("#localImage").bind("change", function(event) {
            var file = this.files[0];

            loadFile(file);
        });
```

This unfortunately is not without it's issues. The File API is very much bleeding edge at this point and support is somewhat limited. As of this writing Firefox is the only browser which features this in production (Version 3.6). Support [landed](http://trac.webkit.org/changeset/59162) in the WebKit trunk literally earlier this month and can be used in their nightlies, but so far has not made it into any production releases.

The site is currently designed to progressively enhance as it detects support for the File API, so no need to worry about being left out of the site as a whole if you are not on one of these browsers yet.

### History

After loading the content using the File API, the original implementation utilized the same #hash storage method for the data URIs, but this proved to be problematic as these strings can become quite large and interacting with these URLs was unwieldily. Needing another data store and being forced to maintain a cache of all local images due to the security model employed by the File API, we were left the options of storing all data in Javascript space or using the new Web Storage APIs implemented as part of HTML5.

Examining both options it seemed the the best course was to utilize the `sessionStorage` object when available and fail over to the javascript data model when not.

```javascript
    // Check for browser support of session storage and that it is accessible
    // This may be inaccessible under certain contexts such as file://
    function supportsSessionStorage() {
        try {
            return !!window.sessionStorage;
        } catch (err) {
            return false;
        }
    }
    var localDataBinding = (function() {
        if (supportsSessionStorage()) {
            // If they support FileReader they really should support storage... but who knows (With the exception of file://)
            return {
                storeImage: function(name, data) {
                    var entryId = (parseInt(sessionStorage["imageList-count"])||0)+1;
                    sessionStorage["imageList-count"] = entryId;
                    sessionStorage["imageList-src-" + entryId] = data;
                    sessionStorage["imageList-display-" + entryId] = name;
                    return entryId;
                },
                getImage: function(entryId) {
                    return { src: sessionStorage["imageList-src-" + entryId], displayName: sessionStorage["imageList-display-" + entryId] };
                }
            };
        } else {
            // Fail over to plain js structures, meaing that refresh, etc will cause failures.
            var cache = [];
            return {
                storeImage: function(name, data) {
                    cache.push({ src: data, displayName: name });
                    return cache.length-1;
                },
                getImage: function(entryId) {
                    return cache[entryId];
                }
            };
        }
    })();
```

Working with this API it seems as though it is still somewhat rough. The vendor documentation all state that supported datatypes are only string values ([Mozilla](https://developer.mozilla.org/en/dom/storage), [WebKit](http://developer.apple.com/safari/library/documentation/AppleApplications/Reference/WebKitDOMRef/Storage_idl/Classes/Storage/index.html)), but the spec implies that this will change be final release and allow for storage of a much broader set of datatypes.

A consequence of this design is that boomark and URL sharing for local files is not a possibility. For users who need to share or store  application states the image in question will need to be stored on a HTTP server and accessed by this route.

These changes as well as some incremental changes have been pushed live on [border-image.com](http://border-image.com) and are available in the github [repository](http://github.com/kpdecker/border-image-generator).
