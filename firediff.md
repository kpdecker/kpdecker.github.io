---
layout: page
title: Firediff
date: 2009-11-01 15:18:38.000000000 -06:00
---

Firediff is a Firebug extension that tracks changes to a pages DOM and CSS.

## Change Tracking


{% include image.html
    src="assets/firediff_small.png"
    alt="Change Tracking Screenshot"
    width="452"
    height="245"
%}

Firediff implements a change monitor that records all of the changes made by firebug and the application itself to CSS and the DOM. This provides insight into the functionality of the application as well as provide a record of the changes that were required to debug and tweak the page's display.

In addition to monitoring each change, Firediff also allows individual changes or sets of changes to be reverted, while leaving other changes intact.

## Snapshot

{% include image.html
    src="assets/firediff_dom_snapshot_small.PNG"
    alt="DOM Snapshot"
    width="180"
    height="95"
%}

Also implemented within Firediff is a series of features that allow for a developer or designer to easily import their changes made in Firebug back into the source for their site or application. These features include the snapshot UI and save snapshot as well as the save diff functionality.

Through these collective snapshot features, the developer or designer can easily visualize and save all of the changes that have been made to the page, be it the current page state or any change in between.

## Dependencies
Firediff 1.1 requires Firebug 1.6 and Firefox 3.6.

## Download
The latest release of Firediff is available [here](http://www.incaseofstairs.com/download/firediff/?C=M).

## Contribution
For those who wish to contribute, the source code is available on googlecode within the [Firebug project](http://code.google.com/p/fbug/) under the [diff tree](http://fbug.googlecode.com/svn/diff). The current release is maintained in the firediff1.0 branch.

Issues may be reported to the [fbug issue tracker](http://code.google.com/p/fbug/issues/list).
