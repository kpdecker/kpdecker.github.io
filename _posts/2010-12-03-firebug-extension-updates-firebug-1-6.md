---
layout: post
title: 'Firebug Extension Updates: Firebug 1.6'
categories:
- Firediff
- Firefocus
tags:
- Firebug
- Firediff
- Firefocus
- Web Development
---

As many of you are already aware, the recent release of <a href="http://blog.getfirebug.com/2010/11/29/firebug-1-6-0/">Firebug 1.6</a> introduced some API changes that caused breakages within <a href="http://www.incaseofstairs.com/firediff/">Firediff</a>. To fix these some minor code changes within Firediff were necessary, thus the 1.1 release that has just been published.

Along with the updates for the new APIs, this version also includes:
- Copy support
- Bug fixes for Firebug detached mode
- Change monitor context menu fixes
- Slight adjustment to the colors used within the text diffs

There are a few support changes in this release. Rather than trying to support multiple versions of both Firebug and Gecko, official testing is limited to a single version of Firebug, 1.6, and as a consequence for Firebug 1.6 to a single version of Firefox, 3.6. This is simply due to the increased QA time that is required to test all of the permutations of Firebug version, Gecko version, and OS type. When updating for the changed APIs in Firebug 1.6 attempts were made to maintain compatibility with Firebug 1.5, but this configuration is no longer officially tested.

Since I was going to through the release process for Firediff I decided to release the 1.2 version of <a href="http://www.incaseofstairs.com/firefocus/">Firefocus</a> as well. This version implements the uk-UA locale but is otherwise the same as 1.1.</p>

Firediff 1.1 is available <a href="http://www.incaseofstairs.com/download/firediff/firediff1.1.xpi">here</a> on incaseofstairs and is currently under review on <a href="https://addons.mozilla.org/en-US/firefox/addon/13179/">AMO</a>.<br />
Firefocus 1.2 is available <a href="http://www.incaseofstairs.com/download/firefocus/firefocus1.2.xpi">here</a> on incaseofstairs and is currently under review on <a href="https://addons.mozilla.org/en-US/firefox/addon/49662/">AMO</a>.

Thanks to everyone who notified me of the breakage when 1.6 was released. It's great to hear feedback from users and to hear that the project is in active use! As always please do not hesitate to let me know how the project is working for you, good or bad.

