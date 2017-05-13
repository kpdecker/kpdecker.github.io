---
layout: post
title: 'Implementation: Facebook for webOS Add Tag UI'
date: 2010-06-04 17:29:40.000000000 -05:00
categories:
- webOS
tags:
- Facebook
- Mobile Development
- Palm
- Web Development
- webOS
---
<p>[caption id="attachment_299" align="alignright" width="240" caption="Facebook Add Tag Interface"]<a href="http://static.incaseofstairs.com/uploads/2010/06/add-tag.png"><img src="{{ site.baseurl }}/assets/add-tag.png" alt="Add Tag Interface" title="Add Tag Interface" width="240" height="360" class="size-full wp-image-299" /></a>[/caption]</p>
<p>
Yesterday we released <a href="http://developer.palm.com/blog/2010/06/facebook-1-2-5-beta-photo-tagging-and-more/">Facebook for webOS 1.2.5 Beta</a> which brings many improvements to our photo functionality including tagging support. Now that the release is out the door, I'd like to take some time to discuss the cooler aspects of the implementation particularly the Add Tag UI.</p>
<h3>Design</h3>
<p>
The core layout of the Add Tag UI is an overlay container containing a fixed position header and a scrolling list below that. The entire screen is covered by these two elements, with the header section maintain its height and the content section expanding to cover the remainder.</p>
<p>
This fixed header layout is fairly common throughout the app, the most common example being the navbar used in the majority of the application's scenes, and is fairly easy to solve if the header is a fixed height and the content scrolls under the header. All that needs to be done is to position the header control using <code>position: fixed</code> and apply a <code>margin-top</code> to the list such that the first element in the list appears at the bottom of the fixed element.</p>
<p>
I've never really like this solution as it requires that the header height be fixed and the list section be "aware" of the header rather than being only concerned about it's own layout. With this in mind (and also wanting to play around with some of the cool things that WebKit offers but I have not been able to use on projects due to the IE factor), I decided to try out the flexible box layout type in webOS.</p>
<h3>Flexible Box Layout</h3>
<p>
To provide some background, one of the key goals of the <a href="http://www.w3.org/TR/css3-flexbox/">flexible box layout module</a> is the ability to specify the size and growth of an element relative to both it's siblings and it's parent. While this was previously possible by using percentage layouts, each of the elements had to be aware of the size that their siblings expected and all elements needed to use the same relative system.</p>
<p>
This is not the case with this layout scheme. Rather than defining a percentage of the container size, elements are defined with an affinity for the excess space in the container. This means that the layout first attempts to fit everything as it would normally layout, then adjusts each element based on the difference in size between the children and the parent. In practice this can generate much more stable, but still fluid layouts when creating applications in HTML+CSS.</p>
<p>
The <code>box-flex</code> (<a href="https://developer.mozilla.org/en/CSS/-moz-box-flex">-moz-box-flex</a>, <a href="http://developer.apple.com/safari/library/documentation/AppleApplications/Reference/SafariCSSRef/Articles/StandardCSSProperties.html#//apple_ref/doc/uid/TP30001266--webkit-box-flex">-webkit-box-flex</a>) is used to define the resize affinity.</p>
<p>
Values here can range from zero to any positive decimal value. When the value is zero, the element will not change size based on extra or lack of space in its parent. When this value is larger than zero the element is considered flexible. This means that when the layout engine determines that the parent box is either overflowed or underflowed these elements will expand or shrink such that all of the children fill the container. The differences in magnitudes of this value determines how the flexible elements are resized.</p>
<p>
As an example a vertically oriented flexible box with 3 children whose flex values are 0, 1, and 2 and has 60px of open space will add 20px to the second element, 40px to the third element and leave the height of the first unchanged. On the other end of the spectrum if there is an overflow of 60px, the elements will be shrunk by the same dimensions above.</p>
<p>
The flexible box model is <a href="http://caniuse.com/#feat=flexbox">supported</a> by Safari 3+, Chrome 3+, Firefox 3+, and of course webOS. As covering the entirety of the specification was out of the scope of this post I would recommend reading some of the other <a href="http://hacks.mozilla.org/2010/04/the-css-3-flexible-box-model/">excellent</a> <a href="http://www.the-haystack.com/2010/01/23/css3-flexbox-part-1/">sources</a> on this topic as well as the <a href="http://www.w3.org/TR/css3-flexbox/">specification</a> itself.</p>
<h3>Implementation</h3>
<p>
For the Add Tag UI, we take advantage of the flexible overflow case outlined above. This allows the upper section to layout at it's natural size using <code>-webkit-box-flex: 0</code> and the user list section to expand to fill the remainder of the page using <code>-webkit-box-flex: 1</code>. By defining the list section to be a <code>Scroller</code> with an embedded <code>List</code>, we can maintain fixed behavior for the upper section and still allow for an arbitrary number of users.</p>
<p>Implemented this looks something like the following:</p>
<p>[html]<br />
&lt;div class=&quot;tag-selector&quot;&gt;<br />
  &lt;div class=&quot;tag-selector-panel&quot;&gt;<br />
    &lt;div x-mojo-element=&quot;TextField&quot;&gt;&lt;/div&gt;<br />
    &lt;div class=&quot;form-wrapper&quot;&gt;<br />
      &lt;div x-mojo-element=&quot;Button&quot;&gt;&lt;/div&gt;<br />
      &lt;div x-mojo-element=&quot;Button&quot;&gt;&lt;/div&gt;<br />
    &lt;/div&gt;<br />
  &lt;/div&gt;<br />
  &lt;div class=&quot;tag-scroller&quot; x-mojo-element=&quot;Scroller&quot;&gt;<br />
    &lt;div x-mojo-element=&quot;List&quot;&gt;&lt;/div&gt;<br />
  &lt;/div&gt;<br />
&lt;/div&gt;<br />
[/html]</p>
<p>With the CSS doing most of the heavy lifting:</p>
<p>[css]<br />
.tag-selector {<br />
  position: absolute;<br />
  top: 0;<br />
  left: 0;<br />
  bottom: 0;<br />
  right: 0;</p>
<p>  z-index: 1000;</p>
<p>  display: -webkit-box;<br />
  -webkit-box-orient: vertical;<br />
}</p>
<p>.tag-scroller {<br />
  position: static !important;<br />
  -webkit-box-flex: 1;<br />
}<br />
[/css]</p>
<p>
A live demo for Firefox and Safari/Chrome available is available <a href="http://demo.incaseofstairs.com/flex-box/index.html">here</a>. Note that the demo is running a modified version to handle desktop and cross-browser differences. These are mostly experimental prefix concerns i.e. <code>display: -webkit-box</code> becomes</p>
<p>[css]<br />
    display: -webkit-box;<br />
    display: -moz-box;<br />
    display: box;<br />
[/css]</p>
<p>
Although there are a few hacks in place to force common layout under both WebKit and Gecko (See <a href="https://bugzilla.mozilla.org/show_bug.cgi?id=570036">Bug 570036</a>). Such is life developing with CSS3 :)</p>
<h3>Future Posts</h3>
<p>
As a final note, I'd like to start a series of these posts. Are there any sections of the Facebook app that you would like me to provide an overview of their implementation? Feel free to comment on this post or drop me a line at <a href="mailto:kpdecker@gmail.com">kpdecker@gmail.com</a>.</p>
