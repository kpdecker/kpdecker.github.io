---
layout: post
title: border-image-generator
categories:
- border-image-generator
tags:
- border-image-generator
- Web Development
---
<p>
Update: border-image-generator is not hosted on <a href="http://border-image.com">border-image.com</a>. The previous URLs are configured to redirect to this url.</p>
<p>
<img class="alignright size-full  wp-image-180" title="border-image-generator Example" src="{{ site.baseurl }}/assets/Screen-shot-2010-04-04-at-6.10.36-PM.png" alt="Example of the border-image-generator visual editor. " width="137" height="137" />The CSS3 <code><a href="http://www.w3.org/TR/css3-background/#border-images">border-image</a></code> property allows for some very cool and efficient design but after implementing them in both Mozilla for the Firebug search panel and WebKit for the Palm Facebook notifications badge, I've learned that they can be quite frustrating to properly tweak by hand.
 </p>
<h3>Background</h3>
<p>
For those who have not been exposed to this property, it allows a single image to be used to style the borders and background of a particular element. In the example above, each section will map to a different border, corner, or the content background, allowing for a single element and image to provide styling the previously required significantly more of each.</p>
<p>
Using these properties the following element can be rendered using simple semantic HTML and CSS.</p>
<p><img class="alignnone size-full wp-image-188" title="Rendered Element" src="{{ site.baseurl }}/assets/Screen-shot-2010-04-04-at-6.29.07-PM.png" alt="Element rendered using border-image" width="173" height="88" /></p>
<p>[html]<br />
&lt;div class=&quot;stylishContent&quot;&gt;Some stylish content&lt;/div&gt;<br />
[/html]</p>
<p>[css]<br />
.stylishContent {<br />
    display: inline-block;<br />
    border-width: 27px 27px 27px 27px;<br />
    -moz-border-image: url(http://www.w3.org/TR/css3-background/border.png) 27 27 27 27; -webkit-border-image: url(http://www.w3.org/TR/css3-background/border.png) 27 27 27 27;<br />
    border-image: url(http://www.w3.org/TR/css3-background/border.png) 27 27 27 27;<br />
}<br />
[/css]</p>
<p>
For more detail, John Resig has published an <a href="http://ejohn.org/blog/border-image-in-firefox/">excellent writeup</a> on the Mozilla <a href="https://developer.mozilla.org/En/CSS/-moz-border-image">implementation</a>.</p>
<h3>App</h3>
<p>
As noted above there is a bit of tweaking involved in getting these settings right. To ease some of this pain I've created the border-image-generator project, which allows for WYSIWYG editing of these properties. Rather than manually adjusting each parameter, border-image-generator allows the various parameters involved to be changed visually with instant preview of what these changes will look like in the current browser.</p>
<p>
Seen in action demonstrating Resig's examples <a href="http://www.incaseofstairs.com/border-image-generator/#%7B%22src%22%3A%22http%3A%2F%2Fwww.w3.org%2FTR%2Fcss3-background%2Fborder.png%22%2C%22linkBorder%22%3Atrue%2C%22borderWidth%22%3A%5B8%2C0%2C0%2C0%5D%2C%22imageOffset%22%3A%5B27%2C27%2C27%2C27%5D%2C%22scaleFactor%22%3A3.689655172413793%7D">here</a> and <a href="http://www.incaseofstairs.com/border-image-generator/#%7B%22src%22%3A%22http%3A%2F%2Fejohn.org%2Ffiles%2Fborder-image%2Fiui%2Fiui%2FwhiteButton.png%22%2C%22linkBorder%22%3Atrue%2C%22borderWidth%22%3A%5B0%2C0%2C0%2C0%5D%2C%22imageOffset%22%3A%5B12%2C12%2C12%2C12%5D%2C%22scaleFactor%22%3A4.655172413793103%7D">here</a>.</p>
<h4>Features</h4>
<ul>
<li>WYSIWYG editing of border-image properties</li>
<li>Cross-browser border-image CSS generation</li>
<li>URL-based State (History + Preview in multiple browsers)</li>
</ul>
<p>It can be accessed through any of the following URLs:</p>
<ul>
<li><a href="http://border-image.com/">border-image</a></li>
<li><a href="http://github.com/kpdecker/border-image-generator">github/kpdecker/border-image-generator</a></li>
</ul>
<p>
Any issues or suggestions for improvements can be sent to myself or logged in the github <a href="http://github.com/kpdecker/border-image-generator/issues">issue tracker</a>.</p>
<h3>Warning</h3>
<p>
As a preliminary warning this is currently a vendor experimental feature but it is currently set to be included in the CSS3 specification. There are some significant differences between the vendor implementations and the W3C candidate, so some caution should be used. Border-image-generator attempts to handle these cases as defined by the spec, but many things could change between the current implementations and the release of CSS3 implementations in the wild.</p>
<h3>Related Links:</h3>
<ul>
<li><a href="https://developer.mozilla.org/En/CSS/-moz-border-image">MDC -moz-border-image Property</a></li>
<li><a href="http://developer.apple.com/safari/library/documentation/AppleApplications/Reference/SafariCSSRef/Articles/StandardCSSProperties.html#//apple_ref/doc/uid/TP30001266--webkit-border-image">ADC -webkit-border-image Documentation</a></li>
<li><a href="http://www.w3.org/TR/css3-background/#border-images">W3C Standard Documentation</a></li>
<li><a href="http://ejohn.org/blog/border-image-in-firefox/">John Resig's article</a></li>
<li><a href="http://www.suburban-glory.com/blog?page=111">Suburban Glory's article</a></li>
</ul>
