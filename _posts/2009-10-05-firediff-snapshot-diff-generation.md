---
layout: post
title: Firediff Snapshot + Diff Generation
categories:
- Firediff
- Firefox
tags:
- Firebug
- Firediff
- Web Development
---

Firediff recently hit a major milestone, with the implementation of major portions of the save snapshot and save diff features. These as well as the revert and search features are available in the most recent [alpha](/download/firediff/firediff1.0a1.xpi).

As a somewhat preemptive warning, the save diff feature is not likely to generate a line for line match that can be applied directly to the source files. This is due a number of reasons, including performance optimizations in Firefox that cull information that is not vital to the final rendering of the page and the large number of coding styles that exist in the wild.

We are currently investigating the options that we have to improve this, although the current iteration should be treated as a  a serialized version of the page state that the designer or developer can use to manually update the source files, rather than something that can be automatically applied to the source using patch or another method.

Feel free to comment on this post or email me at kpdecker AT gmail.com with any input on these features or any of the other Firediff features.
