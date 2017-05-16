---
layout: post
title: border-image-generator
categories:
- border-image-generator
tags:
- border-image-generator
- Web Development
---

Update: border-image-generator is not hosted on [border-image.com](http://border-image.com). The previous URLs are configured to redirect to this url.

![Example of the border-image-generator visual editor.]({{ site.baseurl }}/assets/Screen-shot-2010-04-04-at-6.10.36-PM.png)

The CSS3 [`border-image`](http://www.w3.org/TR/css3-background/#border-images) property allows for some very cool and efficient design but after implementing them in both Mozilla for the Firebug search panel and WebKit for the Palm Facebook notifications badge, I've learned that they can be quite frustrating to properly tweak by hand.
 
### Background

For those who have not been exposed to this property, it allows a single image to be used to style the borders and background of a particular element. In the example above, each section will map to a different border, corner, or the content background, allowing for a single element and image to provide styling the previously required significantly more of each.

Using these properties the following element can be rendered using simple semantic HTML and CSS.

![Element rendered using border-image]({{ site.baseurl }}/assets/Screen-shot-2010-04-04-at-6.29.07-PM.png)

```html
<div class="stylishContent">Some stylish content</div>
```

```css
.stylishContent {
    display: inline-block;
    border-width: 27px 27px 27px 27px;
    -moz-border-image: url(http://www.w3.org/TR/css3-background/border.png) 27 27 27 27; -webkit-border-image: url(http://www.w3.org/TR/css3-background/border.png) 27 27 27 27;
    border-image: url(http://www.w3.org/TR/css3-background/border.png) 27 27 27 27;
}
```

For more detail, John Resig has published an [excellent writeup](http://ejohn.org/blog/border-image-in-firefox/) on the Mozilla [implementation](https://developer.mozilla.org/En/CSS/-moz-border-image).

### App

As noted above there is a bit of tweaking involved in getting these settings right. To ease some of this pain I've created the border-image-generator project, which allows for WYSIWYG editing of these properties. Rather than manually adjusting each parameter, border-image-generator allows the various parameters involved to be changed visually with instant preview of what these changes will look like in the current browser.

Seen in action demonstrating Resig's examples [here](http://www.incaseofstairs.com/border-image-generator/#%7B%22src%22%3A%22http%3A%2F%2Fwww.w3.org%2FTR%2Fcss3-background%2Fborder.png%22%2C%22linkBorder%22%3Atrue%2C%22borderWidth%22%3A%5B8%2C0%2C0%2C0%5D%2C%22imageOffset%22%3A%5B27%2C27%2C27%2C27%5D%2C%22scaleFactor%22%3A3.689655172413793%7D) and [here](http://www.incaseofstairs.com/border-image-generator/#%7B%22src%22%3A%22http%3A%2F%2Fejohn.org%2Ffiles%2Fborder-image%2Fiui%2Fiui%2FwhiteButton.png%22%2C%22linkBorder%22%3Atrue%2C%22borderWidth%22%3A%5B0%2C0%2C0%2C0%5D%2C%22imageOffset%22%3A%5B12%2C12%2C12%2C12%5D%2C%22scaleFactor%22%3A4.655172413793103%7D).

#### Features

- WYSIWYG editing of border-image properties
- Cross-browser border-image CSS generation
- URL-based State (History + Preview in multiple browsers)


It can be accessed through any of the following URLs:

- [border-image](http://border-image.com/)
- [github/kpdecker/border-image-generator](http://github.com/kpdecker/border-image-generator)

Any issues or suggestions for improvements can be sent to myself or logged in the github [issue tracker](http://github.com/kpdecker/border-image-generator/issues).

### Warning

As a preliminary warning this is currently a vendor experimental feature but it is currently set to be included in the CSS3 specification. There are some significant differences between the vendor implementations and the W3C candidate, so some caution should be used. Border-image-generator attempts to handle these cases as defined by the spec, but many things could change between the current implementations and the release of CSS3 implementations in the wild.

### Related Links:

- [MDC -moz-border-image Property](https://developer.mozilla.org/En/CSS/-moz-border-image)
- [ADC -webkit-border-image Documentation](http://developer.apple.com/safari/library/documentation/AppleApplications/Reference/SafariCSSRef/Articles/StandardCSSProperties.html#//apple_ref/doc/uid/TP30001266--webkit-border-image)
- [W3C Standard Documentation](http://www.w3.org/TR/css3-background/#border-images)
- [John Resig's article](http://ejohn.org/blog/border-image-in-firefox/)
- [Suburban Glory's article](http://www.suburban-glory.com/blog?page=111)
