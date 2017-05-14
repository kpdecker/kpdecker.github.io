---
layout: post
title: Cross Platform Text-Indexer
date: 2010-11-25 13:05:29.000000000 -06:00
categories:
- Web Dev
- webOS
tags:
- Mobile Development
- Performance
- Web Development
- webOS
---

While working on a recent Facebook release we ran into a performance bottleneck within the `Mojo.Format.runTextIndexer` API. Performance on device was on the order of a couple of seconds to process the content for feed items 30-50. This combined with the other tasks in the formatting and rendering cycle led to poor performance in the news stream.

For those who are not familiar with the `Mojo.Format.runTextIndexer` API, this API scans human generated text for URLs, phone numbers and email addresses, replacing the plain text representation with HTML links to the particular object. Additionally this API will replace emoticons with image representations on supporting platforms.

With the news stream scene being the primary scene in the application it was vital that we make this as performant as possible so I spent some time profiling this and determined that the bulk of the time in this operation was spent in the generic C indexer logic. Since at it's core this is just a text scanning heuristic that, for the most part, does not rely on any C-level constructs, I decided to investigate a Javascript implementation to see if this would be more performant.

The result was a Javascript implementation that was many magnitudes faster than the core implementation (multiple seconds vs. ~10ms for a variety of Facebook news feeds), allowing for much faster execution as well as the creation of the library being released today.

While this library is designed primarily for webOS applications, it has also been designed to work in cross-platform environments for all functionality other than the emoticon replacement which requires platform specific logic to determine the proper image to replace.

### Implementation

There are two key components to this implementation, the cross platform link indexer and the platform specific emoticon processor.

The link indexer is a two-stage regular expression, using the quite daunting [INDEXER_REGEX](https://github.com/kpdecker/text-indexer/blob/d8c4f396d90300ef63846a132462508f23d72265/text-indexer.js#L22) regex to extract possible linker tokens with Javascript being used to determine the meaning of each token (or throwing it out just text content).

The emoticon processor is implemented by scanning for known, or possibly known, emoticons using the [EMOTICON_REGEX](https://github.com/kpdecker/text-indexer/blob/d8c4f396d90300ef63846a132462508f23d72265/text-indexer.js#L6) regex and then passing these tokens to the platform's text indexer implementation. Doing this allows for our own custom implementation while retaining the look and feel of emoticons used by the rest of the platform. For non-Mojo platforms this logic is disabled and emoticons will be left unmodified, with minimal change.

### Warnings
#### HTML Content

Like the API this aims to replace, this API does not handle HTML context when replacing content. As a consequence this algorithm can break HTML content. In order to prevent this, the input and outputs for the `TextIndxer.run` API should be considered text or minimal HTML that will not match any of the replacements.

It is still possible to use this with content containing HTML by processing in a manner similar to the following although the input still needs to be filtered for XSS and other security concerns.

```javascript
var srcText = $(this).val(),
    womb = $('<div>' + srcText + '<div>');

womb.contents()
    .filter(function() {
        return this.nodeType === Node.TEXT_NODE;
    })
    .each(function() {
        var text = $(this),
            indexedText = TextIndexer.run(text.text()),
            womb = $('<div>' + indexedText + '<div>');
        womb.contents().each(function() {
            text.before(this);
        });

        text.remove();
    });

$('#previewContent').html(womb.html());
```

#### Framework Override

By default this library overrides the `Mojo.Format.runTextIndexer` API (when used within the Mojo framework). Care should be taken with each OS upgrade to ensure that this override does not break any expected behavior. If uncomfortable with this override then it can be removed by removing these lines from the library and using the `TextIndexer.run` API directly.

```javascript
// Mojo Framework override. Unused on non-Mojo platforms and may be removed if undesired in Mojo apps
if (window.Mojo &amp;&amp; Mojo.Format) {
    // Override the Mojo API if it exists in this context.
    Mojo.Format.runTextIndexer = TextIndexer.run;
}
```

#### Code

The code is available on Github within the [text-indexer](https://github.com/kpdecker/text-indexer) repository. A cross-platform live demo is also available [here](http://demo.incaseofstairs.com/indexer/index.html).
